---
title: 'Beitreten innerhalb des Zeitfensters: Azure Daten-Explorer'
description: In diesem Artikel wird das beitreten innerhalb des Zeitfensters in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b1f951f23587451d62deefa5feb24e2d1fc6b612
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251112"
---
# <a name="time-window-join"></a>Zeitfenster-Join

Häufig ist es hilfreich, sich zwischen zwei großen Datasets mit einem High-Cardinality-Schlüssel zu verbinden. wie z. b. eine Vorgangs-ID oder eine Sitzungs-ID, und schränken Sie die Datensätze auf der rechten Seite ($right) weiter ein, die mit jedem linken $Left Datensatz abgeglichen werden müssen, indem Sie auf der linken Seite und auf der rechten Seite eine Einschränkung für den "Zeitabstand" zwischen DateTime-Spalten hinzufügen.

Die-Funktion ist in einem Join nützlich, wie im folgenden Szenario:
* Verknüpfen von zwei großen Datasets gemäß einem High-Cardinality-Schlüssel, z. b. eine Vorgangs-ID oder eine Sitzungs-ID.
* Schränken Sie die Datensätze auf der rechten Seite ($right) ein, die mit jedem linken ($Left) Datensatz abgeglichen werden müssen, indem Sie auf der linken Seite und auf der rechten Seite eine Einschränkung für den "Zeitabstand" zwischen DateTime-Spalten hinzufügen.

Der obige Vorgang unterscheidet sich vom üblichen Kusto-Joinvorgang, da *`equi-join`* das System für den Teil der Übereinstimmung des High-Cardinality-Schlüssels zwischen den linken und rechten Datasets auch eine Entfernungs Funktion anwenden und verwenden kann, um den Join beträchtlich zu beschleunigen.

> [!NOTE]
> Eine Distance-Funktion verhält sich nicht wie Gleichheit (d. h., wenn sowohl dist (x, y) als auch dist (y, z) true sind, folgt nicht, dass dist (x, z) ebenfalls "true" ist.) Intern wird dies als "diagonaler Join" bezeichnet.

Wenn Sie z. b. Ereignis Sequenzen innerhalb eines relativ kleinen Zeitfensters identifizieren möchten, gehen Sie davon aus, dass Sie über eine Tabelle `T` mit dem folgenden Schema verfügen:

* `SessionId`: Eine Spalte vom Typ `string` mit Korrelations-IDs.
* `EventType`: Eine Spalte vom Typ `string` , die den Ereignistyp des Datensatzes identifiziert.
* `Timestamp`: Eine Spalte vom Typ `datetime` gibt an, wann das durch den Datensatz beschriebene Ereignis aufgetreten ist.

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

Unsere Abfrage sollte die folgende Frage beantworten:

   Alle Sitzungs-IDs ermitteln, in denen `A` ein Ereignistyp in `B` einem Zeitfenster auf den Ereignistyp folgt `1min` .

> [!NOTE]
> In den obigen Beispiel Daten ist nur diese Sitzungs-ID `0` .

Semantisch wird diese Frage von der folgenden Abfrage beantwortet, wenn dies ineffizient ist.

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

Schreiben Sie die Abfrage so um, dass die `datetime` Werte in Bucket diskretisiert werden, deren Größe der Hälfte der Größe des Zeitfensters entspricht. Verwenden Sie Kusto *`equi-join`* zum Vergleichen dieser Bucket-IDs.

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
