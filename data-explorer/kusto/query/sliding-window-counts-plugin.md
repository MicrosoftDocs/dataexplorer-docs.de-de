---
title: sliding_window_counts-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird sliding_window_counts-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: feab3d0e8f548817be12f202eb2d494bd65aa133
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507496"
---
# <a name="sliding_window_counts-plugin"></a>sliding_window_counts Plugin

Berechnet die Anzahl und die unterschiedliche Anzahl von Werten in einem Schiebefenster über eine Lookback-Periode mithilfe der [hier](samples.md#performing-aggregations-over-a-sliding-window)beschriebenen Technik .

Berechnen Sie z. B. für jeden *Tag*die Anzahl und die unterschiedliche Anzahl der Benutzer in der *Vorwoche*. 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

**Syntax**

*T* `| evaluate` `,` *Start* `,` *End* `,` `,` *Bin* `,` `,` `,` *IdColumn* `,` *TimelineColumn* *LookbackWindow* *dim1* *dim2* IdColumn TimelineColumn Start End LookbackWindow Bin [ dim1 dim2 ...] `sliding_window_counts(``)`

**Argumente**

* *T*: Der tabellarische Eingabeausdruck.
* *IdColumn*: Der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *TimelineColumn*: Der Name der Spalte, die die Zeitachse darstellt.
* *Start*: Skalar mit Wert des Analysestartzeitraums.
* *Ende*: Skalar mit Wert der Analyseendperiode.
* *LookbackWindow*: Skalar konstanter Wert der Lookback-Periode (z.B. für dcount-Benutzer in der Vergangenheit 7d: LookbackWindow = 7d)
* *Bin*: Skalar-Konstantenwert des Analyseschrittzeitraums. Kann entweder ein numerischer/datumszeitlicher/zeitstempel-Wert sein, oder eine Zeichenfolge, die `week` / `month` / `year`eine von ist, in diesem Fall werden alle Perioden Anfang des[Monatsbeginns](startofyearfunction.md) des[Monats](startofmonthfunction.md)/entsprechend [beginnen.](startofweekfunction.md)/ 
* *dim1*, *dim2*, ...: (optional) Liste der Dimensionsspalten, die die Berechnung der Aktivitätsmetriken aufteilen.

**Rückgabe**

Gibt eine Tabelle zurück, die die Zähl- und eindeutigen Zählwerte von IDs in der Nachwartezeit, für jede Zeitachsenperiode (nach Lagerplatz) und für jede vorhandene Dimensionskombination enthält.

Ausgabetabellenschema ist:

|*TimelineColumn*|dim1|..|dim_n|Anzahl|Dcount|
|---|---|---|---|---|---|
|Typ: ab *TimelineColumn*|..|..|..|long|long|


**Beispiele**

Berechnen Sie die Anzahl und Anzahl der Anzahln für Benutzer in der vergangenen Woche für jeden Tag im Analysezeitraum. 

```kusto
let start = datetime(2017 - 08 - 01);
let end = datetime(2017 - 08 - 07); 
let lookbackWindow = 3d;  
let bin = 1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'Bob',      datetime(2017 - 08 - 01), 
'David',    datetime(2017 - 08 - 01), 
'David',    datetime(2017 - 08 - 01), 
'John',     datetime(2017 - 08 - 01), 
'Bob',      datetime(2017 - 08 - 01), 
'Ananda',   datetime(2017 - 08 - 02),  
'Atul',     datetime(2017 - 08 - 02), 
'John',     datetime(2017 - 08 - 02), 
'Ananda',   datetime(2017 - 08 - 03), 
'Atul',     datetime(2017 - 08 - 03), 
'Atul',     datetime(2017 - 08 - 03), 
'John',     datetime(2017 - 08 - 03), 
'Bob',      datetime(2017 - 08 - 03), 
'Betsy',    datetime(2017 - 08 - 04), 
'Bob',      datetime(2017 - 08 - 05), 
];
T | evaluate sliding_window_counts(UserId, Timestamp, start, end, lookbackWindow, bin)


```

|Timestamp|Anzahl|Dcount|
|---|---|---|
|2017-08-01 00:00:00.0000000|5|3|
|2017-08-02 00:00:00.0000000|8|5|
|2017-08-03 00:00:00.0000000|13|5|
|2017-08-04 00:00:00.0000000|9|5|
|2017-08-05 00:00:00.0000000|7|5|
|2017-08-06 00:00:00.0000000|2|2|
|2017-08-07 00:00:00.0000000|1|1|