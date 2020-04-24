---
title: activity_counts_metrics-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird activity_counts_metrics-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e55cd940850440499d227082f57769e499e6a551
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519311"
---
# <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics-Plug-In

Berechnet nützliche Aktivitätsmetriken (Gesamtanzahlwerte, unterschiedliche Zählwerte, unterschiedliche Anzahl neuer Werte, aggregierte eindeutige Anzahl) für jedes Zeitfenster, das mit *allen* vorherigen Zeitfenstern verglichen/aggregiert wird (im Gegensatz zu [activity_metrics-Plugin,](activity-metrics-plugin.md) in dem jedes Zeitfenster nur mit dem vorherigen Zeitfenster verglichen wird).

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**Syntax**

*T* `| evaluate` `,` *Start* `,` *End* `,` *Window* `,` `,` `,` `,` *IdColumn* `,` *TimelineColumn* *Cohort* *dim2* *dim1* IdColumn TimelineColumn Start End Window [ Kohorte ] [ dim1 dim2 ...] `activity_counts_metrics(` [`,` *Rückblick*]`)`

**Argumente**

* *T*: Der tabellarische Eingabeausdruck.
* *IdColumn*: Der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *TimelineColumn*: Der Name der Spalte, die die Zeitachse darstellt.
* *Start*: Skalar mit Wert des Analysestartzeitraums.
* *Ende*: Skalar mit Wert der Analyseendperiode.
* *Fenster*: Skalar mit Wert des Analysefensterzeitraums. Kann entweder ein numerischer/datumszeitlicher/zeitstempel-Wert sein, oder eine Zeichenfolge, die `week` / `month` / `year`eine von ist, in diesem Fall werden alle Perioden Anfang des[Monatsbeginns](startofyearfunction.md) des[Monats](startofmonthfunction.md)/entsprechend [beginnen.](startofweekfunction.md)/ 
* *dim1*, *dim2*, ...: (optional) Liste der Dimensionsspalten, die die Berechnung der Aktivitätsmetriken aufteilen.

**Rückgabe**

Gibt eine Tabelle mit den Gesamtzählwerten, unterschiedlichen Zählwerten, deutlicher Anzahl neuer Werte und aggregierter eindeutiger Anzahl für jedes Zeitfenster zurück.

Ausgabetabellenschema ist:

|TimelineColumn|dim1|...|dim_n|count|dcount|new_dcount|aggregated_dcount
|---|---|---|---|---|---|---|---|---|
|Typ: ab *TimelineColumn*|..|..|..|long|long|long|long|long


* *TimelineColumn*: Startzeit des Zeitfensters.
* *count*: Die Gesamtzahl der Datensätze im Zeitfenster und *dim(s)*
* *dcount*: Die unterschiedlichen ID-Werte zählen im Zeitfenster und *dim(s)*
* *new_dcount*: Die unterschiedlichen ID-Werte im Zeitfenster und *Dim(s)* im Vergleich zu allen vorherigen Zeitfenstern. 
* *aggregated_dcount*: Die aggregierten id-Gesamtwerte von *Dim(s)* vom 1. Zeitfenster bis zum aktuellen (inklusive).

**Beispiele**

### <a name="daily-activity-counts"></a>Anzahl der täglichen Aktivitäten 

Die nächste Abfrage berechnet die Anzahl der täglichen Aktivität für die bereitgestellte Eingabetabelle.

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

|Timestamp|count|dcount|new_dcount|aggregated_dcount|
|---|---|---|---|---|
|2017-08-01 00:00:00.0000000|4|4|4|4|
|2017-08-02 00:00:00.0000000|3|3|2|6|
|2017-08-03 00:00:00.0000000|6|5|2|8|
|2017-08-04 00:00:00.0000000|1|1|0|8|


