---
title: Beitritt innerhalb des Zeitfensters - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt das Verbinden innerhalb des Zeitfensters in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aa3b81694714ef5af94407cdfdac263af0631e40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513361"
---
# <a name="joining-within-time-window"></a>Verbinden innerhalb des Zeitfensters

Es ist oft sinnvoll, zwei große Datensätze auf einem Schlüssel mit hoher Kardinalität (z. B. einer Vorgangs-ID oder einer Sitzungs-ID) zu verbinden und die Datensätze auf der rechten Seite (`$right`) weiter einzuschränken, die für jeden Datensatz auf der linken Seite (`$left`) übereinstimmen müssen, indem eine Einschränkung der "Zeitentfernung" zwischen `datetime` Spalten auf der linken und rechten Seite hinzugefügt wird. Dies unterscheidet sich von der üblichen Kusto-Join-Bedienung, da das System neben dem "Äqui-Join"-Teil (passend zum Hochkardinalschlüssel oder dem linken und rechten Datensatz) auch eine Distanzfunktion anwenden und damit die Verknüpfung erheblich beschleunigen kann. Beachten Sie, dass sich eine Entfernungsfunktion nicht `dist(x,y)` wie `dist(y,z)` Gleichheit verhält (d. h., wenn beide und wahr sind, folgt sie nicht, was `dist(x,z)` auch wahr ist.) *Intern bezeichnen wir dies manchmal als "diagonaljoin".*

Angenommen, wir möchten Ereignissequenzen innerhalb eines relativ kleinen Zeitfensters identifizieren. Um dieses Beispiel zu veranschaulichen, gehen wir davon aus, dass wir eine Tabelle `T` mit dem folgenden Schema haben:

- `SessionId`: Eine Spalte `string` des Typs mit Korrelations-IDs.
- `EventType`: Eine Spalte `string` des Typs, die den Ereignistyp des Datensatzes identifiziert.
- `Timestamp`: Eine Spalte `datetime` des Typs, die angibt, wann das vom Datensatz beschriebene Ereignis eingetreten ist.

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

Unsere Anfrage soll mit unserer Anfrage die folgende Frage beantworten:

   Suchen Sie alle Sitzungs-IDs, auf die `B` `1min` ein Ereignistyp `A` innerhalb des Zeitfensters gefolgt wurde.

(In den obigen Beispieldaten ist `0`die einzige Sitzungs-ID .)

Semantisch beantwortet die folgende Abfrage diese Frage, wenn auch ineffizient:

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

Um diese Abfrage zu optimieren, können wir sie wie unten beschrieben neu schreiben, sodass das Zeitfenster als Verknüpfungsschlüssel ausgedrückt wird.

**Umschreiben der Abfrage, um das Zeitfenster zu berücksichtigen**

Die Idee ist, die Abfrage `datetime` so umzuschreiben, dass die Werte in Buckets "diskretisiert" werden, deren Größe halb so groß ist wie das Zeitfenster.
Wir können dann Kustos Äqui-Join verwenden, um diese Bucket-IDs zu vergleichen.

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


**Runnable Query-Referenz (mit inder Tabelle)**

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


**50M Datenabfrage**

Die nächste Abfrage emuliert einen Datensatz mit 50M-Datensätzen und 10M-IDs und führt die Abfrage mit der oben beschriebenen Technik aus.

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