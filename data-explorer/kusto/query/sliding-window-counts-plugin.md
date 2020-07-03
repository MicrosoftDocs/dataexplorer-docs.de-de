---
title: 'sliding_window_counts-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird sliding_window_counts-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4d90bc3b6222896d45374d771ce5f87f4bdf6786
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85902023"
---
# <a name="sliding_window_counts-plugin"></a>Plug-in sliding_window_counts

Berechnet die Anzahl und die unterschiedliche Anzahl von Werten in einem gleitenden Fenster mithilfe des [hier](samples.md#perform-aggregations-over-a-sliding-window)beschriebenen Verfahrens in einem gleitenden Fenster.

Berechnen Sie beispielsweise für jeden *Tag*die Anzahl und die unterschiedliche Anzahl von Benutzern in der vorherigen *Woche*. 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

**Syntax**

*T* `| evaluate` `sliding_window_counts(` *idColumn* `,` *timelinecolenn* `,` *Start* `,` *Ende* `,` *lookbackwindow* `,` *bin* `,` [*dim1* `,` *dim2* `,` ...]`)`

**Argumente**

* *T*: der tabellarische Eingabe Ausdruck.
* *IdColumn*: der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *Timelinecolren*: der Name der Spalte, die die Zeitachse darstellt.
* *Start*: Skalar mit dem Wert des Start Zeitraums der Analyse.
* *End*: Skalar mit dem Wert des endzeitraums der Analyse.
* *Lookbackwindow*: skalare konstanter Wert des Nachschlage Zeitraums (z `dcount` . b. für Benutzer in der Vergangenheit 7D: lookbackwindow = 7D)
* *Bin*: skalare Konstante Wert des Analyseschritt Zeitraums. Bei diesem Wert kann es sich um einen numerischen Wert/einen DateTime-/Timestampwert handeln. Wenn der Wert eine Zeichenfolge mit dem Format ist `week` / `month` / `year` , werden alle Zeiträume [StartOf Week](startofweekfunction.md)StartOf / [Month](startofmonthfunction.md) / [StartOf Year](startofyearfunction.md). 
* *dim1*, *dim2*,...: (optional) Liste der Dimensions Spalten, in denen die Berechnung der Aktivitäts Metrik in Slice ist.

**Rückgabe**

Gibt eine Tabelle zurück, die die Anzahl-und unterschiedlichen Zählerwerte der IDs im Nachschlage Zeitraum, für jeden Zeitachse (von bin) und für jede vorhandene Dimensions Kombination enthält.

Das Ausgabe Tabellen Schema ist:

|*Timelinecolumschlag*|`dim1`|..|`dim_n`|`count`|`dcount`|
|---|---|---|---|---|---|
|Typ: ab *timelinecolumschlag*|..|..|..|long|long|


**Beispiele**

Berechnen von Anzahlen und `dcounts` für Benutzer in der letzten Woche für jeden Tag im Analysezeitraum. 

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

|Timestamp|Anzahl|`dcount`|
|---|---|---|
|2017-08-01 00:00:00.0000000|5|3|
|2017-08-02 00:00:00.0000000|8|5|
|2017-08-03 00:00:00.0000000|13|5|
|2017-08-04 00:00:00.0000000|9|5|
|2017-08-05 00:00:00.0000000|7|5|
|2017-08-06 00:00:00.0000000|2|2|
|2017-08-07 00:00:00.0000000|1|1|