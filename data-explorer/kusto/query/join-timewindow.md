---
title: 'Beitreten innerhalb des Zeitfensters: Azure Daten-Explorer'
description: In diesem Artikel wird das beitreten innerhalb des Zeitfensters in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d23d76fdcc592435a8ec7fa24ef5d0dfd5186c68
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128457"
---
# <a name="time-window-join"></a>Zeitfenster-Join

Häufig ist es hilfreich, zwischen zwei großen Datasets mit einem High-Cardinality-Schlüssel (z. b. eine Vorgangs-ID oder eine Sitzungs-ID) zu verknüpfen und die Datensätze auf der rechten Seite () weiter einzuschränken `$right` , die für jeden linksseitigen ( `$left` ) Datensatz abgeglichen werden müssen `datetime` Dies unterscheidet sich von einem normalen Kusto-Joinvorgang als. zusätzlich zum Teil "Equi-Join" (Übereinstimmung mit dem High-Cardinality-Schlüssel oder den linken und rechten Datasets) kann das System auch eine Entfernungs Funktion anwenden und verwenden, um den Join beträchtlich zu beschleunigen. Beachten Sie, dass sich eine Entfernungs Funktion nicht wie Gleichheit verhält (d. h., wenn sowohl als `dist(x,y)` `dist(y,z)` auch true ist, wird Sie nicht befolgt, da `dist(x,z)` auch true ist). *Intern wird dies als "diagonaler Join" bezeichnet.*

Nehmen wir beispielsweise an, dass wir Ereignis Sequenzen innerhalb eines relativ kleinen Zeitfensters identifizieren möchten. Um dieses Beispiel zu veranschaulichen, nehmen wir an, dass eine Tabelle `T` mit dem folgenden Schema vorhanden ist:

- `SessionId`: Eine Spalte vom Typ `string` mit Korrelations-IDs.
- `EventType`: Eine Spalte vom Typ `string` , die den Ereignistyp des Datensatzes identifiziert.
- `Timestamp`: Eine Spalte vom Typ, `datetime` die angibt, wann das durch den Datensatz beschriebene Ereignis aufgetreten ist.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
T
```

|SessionID|EventType|Timestamp|
|---|---|---|
|0|Ein|2017-10-01 00:00:00.0000000|
|0|B|2017-10-01 00:01:00.0000000|
|1|B|2017-10-01 00:02:00.0000000|
|1|Ein|2017-10-01 00:03:00.0000000|
|3|Ein|2017-10-01 00:04:00.0000000|
|3|B|2017-10-01 00:10:00.0000000|


**Problembeschreibung**

Wir möchten, dass die Abfrage die folgende Frage beantwortet:

   Alle Sitzungs-IDs, in denen ein Ereignistyp auf `A` einen Ereignistyp folgt, finden Sie `B` im `1min` Zeitfenster.

(In den obigen Beispiel Daten ist nur diese Sitzungs-ID `0` .)

Semantisch wird diese Frage von der folgenden Abfrage beantwortet, wenn dies ineffizient ist:

```kusto
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp
    ) on SessionId
| where (End - Start) between (0min .. 1min)
| project SessionId, Start, End 

```

|SessionID|Start|Ende|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|

Um diese Abfrage zu optimieren, können wir Sie wie unten beschrieben umschreiben, damit das Zeitfenster als joinschlüssel ausgedrückt wird.

**Umschreiben der Abfrage, um das Zeitfenster zu berücksichtigen**

Die Idee besteht darin, die Abfrage so umzuschreiben, dass die `datetime` Werte in Bucket "diskretisiert" werden, deren Größe die Hälfte der Größe des Zeitfensters ist.
Wir können dann den Gleichheitsjoin von Kusto verwenden, um diese Bucket-IDs zu vergleichen.

```kusto
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0; // lookup bin = equal to 1/2 of the lookup window
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp,
          // TimeKey on the left side of the join is mapped to a discrete time axis for the join purpose
          TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              // TimeKey on the right side of the join - emulates event 'B' appearing several times
              // as if it was 'replicated'
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    // 'mv-expand' translates the TimeKey array range into a column
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionID|Start|Ende|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|

**RUNNABLE-Abfrage Verweis (mit Tabellen Inline)**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp, TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionID|Start|Ende|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|


**50 Mio. Datenabfrage**

Die nächste Abfrage emuliert ein DataSet mit 50 Mio. Datensätzen und ~ 10M IDs und führt die Abfrage mit dem oben beschriebenen Verfahren aus.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = range x from 1 to 50000000 step 1
| extend SessionId = rand(10000000), EventType = rand(3), Time=datetime(2017-01-01)+(x * 10ms)
| extend EventType = case(EventType <= 1, "A",
                          EventType <= 2, "B",
                          "C");
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Time, TimeKey = bin(Time, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Time, 
              TimeKey = range(bin(Time-lookupWindow, lookupBin), 
                              bin(Time, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
| count 
```

|Anzahl|
|---|
|1276|
