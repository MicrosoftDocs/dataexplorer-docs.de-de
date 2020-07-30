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
ms.openlocfilehash: df7b994350297d911a0f3be59c791b6538899d67
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349787"
---
# <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics-Plug-In

Berechnet nützliche Aktivitätsmetriken für jedes Zeitfenster, das mit *allen* vorherigen Zeitfenstern verglichen/aggregiert wurde. Folgende Metriken sind enthalten: Gesamtzahl Werte, unterschiedliche Anzahl Werte, unterschiedliche Anzahl neuer Werte und aggregierte unterschiedliche Anzahl. Vergleichen Sie dieses Plug-in mit [activity_metrics-Plug](activity-metrics-plugin.md)-in, wobei jedes Zeitfenster nur mit dem vorherigen Zeitfenster verglichen wird.

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>Syntax

*T* `| evaluate` `activity_counts_metrics(` *idColumn* `,` *timelinecolenn* `,` *Start* `,` *End* `,` *Endfenster* [ `,` *Kohorte*] [ `,` *dim1* `,` *dim2* `,` ...] [ `,` *Lookback*]`)`

## <a name="arguments"></a>Argumente

* *T*: der tabellarische Eingabe Ausdruck.
* *IdColumn*: der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *Timelinecolumzun*: der Name der Spalte, die die Zeitachse darstellt.
* *Start*: Skalar mit dem Wert des Start Zeitraums der Analyse.
* *End*: Skalar mit dem Wert des endzeitraums der Analyse.
* *Fenster*: Skalar mit dem Wert des Analysefenster Zeitraums. Kann ein numerischer/DateTime-/timestamp-Wert oder eine Zeichenfolge sein, die einer von ist `week` / `month` / `year` . in diesem Fall werden alle Zeiträume [starttfweek](startofweekfunction.md) / [starttfmonth](startofmonthfunction.md) oder [starto fyear](startofyearfunction.md). 
* *dim1*, *dim2*,...: (optional) Liste der Dimensions Spalten, in denen die Berechnung der Aktivitäts Metrik in Slice ist.

## <a name="returns"></a>Rückgabe

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

## <a name="examples"></a>Beispiele

### <a name="daily-activity-counts"></a>Anzahl täglicher Aktivitäten 

Die nächste Abfrage berechnet die Anzahl täglicher Aktivitäten für die angegebene Eingabe Tabelle.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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


