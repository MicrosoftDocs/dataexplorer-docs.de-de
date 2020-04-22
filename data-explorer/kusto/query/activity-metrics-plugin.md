---
title: activity_metrics-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird activity_metrics-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fe199636d23f576220772b3a33c96ec8367a1e23
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663925"
---
# <a name="activity_metrics-plugin"></a>activity_metrics-Plug-In

Berechnet nützliche Aktivitätsmetriken (unterschiedliche Zählwerte, unterschiedliche Anzahl neuer Werte, Aufbewahrungsrate und Abwanderungsrate) basierend auf dem aktuellen Periodenfenster im Vergleich zum vorherigen Periodenfenster (im Gegensatz zu [activity_counts_metrics-Plugin,](activity-counts-metrics-plugin.md) in dem jedes Zeitfenster mit *allen* vorherigen Zeitfenstern verglichen wird).

```kusto
T | evaluate activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**Syntax**

*T* `| evaluate` `,` `,` `,` *End*`,``,` `,` `,` *IdColumn* *Start* *Window* *TimelineColumn* *dim2* *dim1* IdColumn TimelineColumn [ Start Ende ] Fenster [ dim1 dim2 ...] `activity_metrics(``)`

**Argumente**

* *T*: Der tabellarische Eingabeausdruck.
* *IdColumn*: Der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *TimelineColumn*: Der Name der Spalte, die die Zeitachse darstellt.
* *Start*: (optional) Skalar mit Wert des Analysestartzeitraums.
* *Ende*: (optional) Skalar mit Wert der Analyseendperiode.
* *Fenster*: Skalar mit Wert des Analysefensterzeitraums. Kann entweder ein numerischer/datumszeitlicher/zeitstempel-Wert sein, oder eine Zeichenfolge, die `week` / `month` / `year`eine von ist, in diesem Fall werden alle Perioden Anfang des[Monatsbeginns](startofyearfunction.md) des[Monats](startofmonthfunction.md)/entsprechend [beginnen.](startofweekfunction.md)/ 
* *dim1*, *dim2*, ...: (optional) Liste der Dimensionsspalten, die die Berechnung der Aktivitätsmetriken aufteilen.

**Rückgabe**

Gibt eine Tabelle zurück, die die unterschiedlichen Zählwerte, die unterschiedliche Anzahl neuer Werte, die Aufbewahrungsrate und die Abwanderungsrate für jede Zeitachsenperiode und für jede vorhandene Dimensionskombination aufweist.

Ausgabetabellenschema ist:

|*TimelineColumn*|dcount_values|dcount_newvalues|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|--|--|--|--|--|--|
|Typ: ab *TimelineColumn*|long|long|double|double|..|..|..|

**Hinweise**

***Definition der Aufbewahrungsrate***

`Retention Rate`über einen Zeitraum wird wie folgt berechnet:

    # of customers returned during the period
    / (divided by)
    # customers at the beginning of the period

wobei `# of customers returned during the period` die als definiert ist wie:

    # of customers at end of period
    - (minus)
    # of new customers acquired during the period

`Retention Rate`kann von 0,0 bis 1,0 variieren  
Die höhere Punktzahl bedeutet die größere Anzahl von wiederkehrenden Benutzern.


***Churn Rate Definition***

`Churn Rate`über einen Zeitraum wird wie folgt berechnet:
    
    # of customers lost in the period
    / (divided by)
    # of customers at the beginning of the period

wobei `# of customer lost in the period` die als definiert ist wie:

    # of customers at the beginning of the period
    - (minus)
    # of customers at the end of the period

`Churn Rate`kann von 0,0 bis 1,0 variieren Die höhere Punktzahl bedeutet, dass die größere Anzahl von Benutzern NICHT zum Dienst zurückkehrt.

***Churn vs. Retentionsrate***

Abgeleitet von der `Churn Rate` `Retention Rate`Definition von und , ist das Folgende immer wahr:

    [Retention rate] = 100.0% - [Churn Rate]


**Beispiele**

### <a name="weekly-retention-rate-and-churn-rate"></a>Wöchentliche Retentionsrate und Abwanderungsrate

Die nächste Abfrage berechnet die Aufbewahrungs- und Abchurzrate für das Wochenfenster.

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// 
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate, churn_rate
| render timechart 
```

|_day|retention_rate|churn_rate|
|---|---|---|
|2017-01-02 00:00:00.0000000|NaN|NaN|
|2017-01-09 00:00:00.0000000|0.179910044977511|0.820089955022489|
|2017-01-16 00:00:00.0000000|0.744374437443744|0.255625562556256|
|2017-01-23 00:00:00.0000000|0.612096774193548|0.387903225806452|
|2017-01-30 00:00:00.0000000|0.681141439205955|0.318858560794045|
|2017-02-06 00:00:00.0000000|0.278145695364238|0.721854304635762|
|2017-02-13 00:00:00.0000000|0.223172628304821|0.776827371695179|
|2017-02-20 00:00:00.0000000|0,38|0.62|
|2017-02-27 00:00:00.0000000|0.295519001701645|0.704480998298355|
|2017-03-06 00:00:00.0000000|0.280387770320656|0.719612229679344|
|2017-03-13 00:00:00.0000000|0.360628154795289|0.639371845204711|
|2017-03-20 00:00:00.0000000|0.288008028098344|0.711991971901656|
|2017-03-27 00:00:00.0000000|0.306134969325153|0.693865030674847|
|2017-04-03 00:00:00.0000000|0.356866537717602|0.643133462282398|
|2017-04-10 00:00:00.0000000|0.495098039215686|0.504901960784314|
|2017-04-17 00:00:00.0000000|0.198296836982968|0.801703163017032|
|2017-04-24 00:00:00.0000000|0.0618811881188119|0.938118811881188|
|2017-05-01 00:00:00.0000000|0.204657727593507|0.795342272406493|
|2017-05-08 00:00:00.0000000|0.517391304347826|0.482608695652174|
|2017-05-15 00:00:00.0000000|0.143667296786389|0.856332703213611|
|2017-05-22 00:00:00.0000000|0.199122325836533|0.800877674163467|
|2017-05-29 00:00:00.0000000|0.063468992248062|0.936531007751938|

:::image type="content" source="images/queries/activity-metrics-churn-and-retention.png" border="false" alt-text="Aktivitätsmetriken abwanderungen und beibehalten":::

### <a name="distinct-values-and-distinct-new-values"></a>Deutliche Werte und unterschiedliche "neue" Werte 

Die nächste Abfrage berechnet unterschiedliche Werte und "neue" Werte (IDs, die im vorherigen Zeitfenster nicht angezeigt wurden) für das Wochenfenster.


```kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// 
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, dcount_values, dcount_newvalues
| render timechart 
```

|_day|dcount_values|dcount_newvalues|
|---|---|---|
|2017-01-02 00:00:00.0000000|630|630|
|2017-01-09 00:00:00.0000000|738|575|
|2017-01-16 00:00:00.0000000|1187|841|
|2017-01-23 00:00:00.0000000|1092|465|
|2017-01-30 00:00:00.0000000|1261|647|
|2017-02-06 00:00:00.0000000|1744|1043|
|2017-02-13 00:00:00.0000000|1563|432|
|2017-02-20 00:00:00.0000000|1406|818|
|2017-02-27 00:00:00.0000000|1956|1429|
|2017-03-06 00:00:00.0000000|1593|848|
|2017-03-13 00:00:00.0000000|1801|1423|
|2017-03-20 00:00:00.0000000|1710|1017|
|2017-03-27 00:00:00.0000000|1796|1516|
|2017-04-03 00:00:00.0000000|1381|1008|
|2017-04-10 00:00:00.0000000|1756|1162|
|2017-04-17 00:00:00.0000000|1831|1409|
|2017-04-24 00:00:00.0000000|1823|1164|
|2017-05-01 00:00:00.0000000|1811|1353|
|2017-05-08 00:00:00.0000000|1691|1246|
|2017-05-15 00:00:00.0000000|1812|1608|
|2017-05-22 00:00:00.0000000|1740|1017|
|2017-05-29 00:00:00.0000000|960|756|

:::image type="content" source="images/queries/activity-metrics-dcount-and-dcount-newvalues.png" border="false" alt-text="Aktivitätsmetriken dcount und dcount neue Werte":::
