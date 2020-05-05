---
title: 'activity_counts_metrics-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird activity_counts_metrics-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b06b1c137552ba19f9b1ef5367a25bb72eea5c93
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742044"
---
# <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics-Plug-In

Berechnet nützliche Aktivitätsmetriken für jedes Zeitfenster, das mit *allen* vorherigen Zeitfenstern verglichen/aggregiert wurde. Folgende Metriken sind enthalten: Gesamtzahl Werte, unterschiedliche Anzahl Werte, unterschiedliche Anzahl neuer Werte und aggregierte unterschiedliche Anzahl. Vergleichen Sie dieses Plug-in mit [activity_metrics-Plug](activity-metrics-plugin.md)-in, wobei jedes Zeitfenster nur mit dem vorherigen Zeitfenster verglichen wird.

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**Syntax**

*T* `| evaluate` `,` *dim1* `,` *dim2* `,` *Start* `,` *Window* `,` *Cohort* *IdColumn* `,` `,` *End* *TimelineColumn* idColumn`,` timelinecolenn-Start Fenster [Kohorte] [dim1 dim2...] `activity_counts_metrics(` [`,` *Lookback*]`)`

**Argumente**

* *T*: der tabellarische Eingabe Ausdruck.
* *IdColumn*: der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *Timelinecolumzun*: der Name der Spalte, die die Zeitachse darstellt.
* *Start*: Skalar mit dem Wert des Start Zeitraums der Analyse.
* *End*: Skalar mit dem Wert des endzeitraums der Analyse.
* *Fenster*: Skalar mit dem Wert des Analysefenster Zeitraums. Kann ein numerischer/DateTime-/timestamp-Wert oder eine Zeichenfolge sein, die `week` / `month` / `year`einer von ist. in diesem Fall werden alle Zeiträume [starttfweek](startofweekfunction.md)/[starttfmonth](startofmonthfunction.md) oder [starto fyear](startofyearfunction.md). 
* *dim1*, *dim2*,...: (optional) Liste der Dimensions Spalten, in denen die Berechnung der Aktivitäts Metrik in Slice ist.

**Rückgabe**

Gibt eine Tabelle zurück, die Folgendes enthält: Gesamtzahl Werte, unterschiedliche Anzahl Werte, unterschiedliche Anzahl neuer Werte und aggregierte unterschiedliche Anzahl für jedes Zeitfenster.

Das Ausgabe Tabellen Schema ist:

|`TimelineColumn`|`dim1`|...|`dim_n`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`
|---|---|---|---|---|---|---|---|---|
|Typ: ab*`TimelineColumn`*|..|..|..|long|long|long|long|long


* *`TimelineColumn`*: Die Startzeit des Zeitfensters.
* *`count`*: Gesamtzahl der Datensätze im Zeitfenster und *Dim (s)*
* *`dcount`*: Die Anzahl der eindeutigen ID-Werte im Zeitfenster und die *Dim (s)*
* *`new_dcount`*: Die eindeutigen ID-Werte im Zeitfenster und *Dim (s)* im Vergleich zu allen vorherigen Zeitfenstern. 
* *`aggregated_dcount`*: Die Gesamtzahl der aggregierten eindeutigen ID-Werte von *Dim (s)* vom ersten Zeitfenster bis zum aktuellen (einschließlich).

**Beispiele**

### <a name="daily-activity-counts"></a>Anzahl täglicher Aktivitäten 

Die nächste Abfrage berechnet die Anzahl täglicher Aktivitäten für die angegebene Eingabe Tabelle.

```kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01), 
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),  
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
 T 
 | evaluate activity_counts_metrics(UserId, Timestamp, start, end, window)
```

|`Timestamp`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`|
|---|---|---|---|---|
|2017-08-01 00:00:00.0000000|4|4|4|4|
|2017-08-02 00:00:00.0000000|3|3|2|6|
|2017-08-03 00:00:00.0000000|6|5|2|8|
|2017-08-04 00:00:00.0000000|1|1|0|8|


