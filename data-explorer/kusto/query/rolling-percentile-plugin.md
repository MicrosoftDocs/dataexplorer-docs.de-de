---
title: rolling_percentile-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird rolling_percentile-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 02def4069c83eeec080ca059493132619fce30d5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510267"
---
# <a name="rolling_percentile-plugin"></a>rolling_percentile Plugin

Gibt eine Schätzung für das angegebene Perzentil der *ValueColumn-Population* in einem rollenden (gleitenden) *BinsPerWindow-Größenfenster* pro *BinSize*zurück.

```kusto
T | evaluate rolling_percentile(ValueColumn, Percentile, IndexColumn, BinSize, BinsPerWindow)
```

**Syntax**

*T* `| evaluate` `,` `,` `,` `,` `,` `,` *ValueColumn* `,` *IndexColumn* *BinSize* *dim2* *Percentile* *dim1* *BinsPerWindow* ValueColumn Perzentil IndexSpalte BinSize BinsPerWindow [ dim1 dim2 ...] `rolling_percentile(``)`

**Argumente**

* *T*: Der tabellarische Eingabeausdruck.
* *ValueColumn*: Der Name der Spalte mit Werten, von der das Perzentil berechnet werden soll. 
* *Perzentil*: Skalar mit dem zu berechnenden Perzentil.
* *IndexColumn*: Der Name der Spalte, über die das rollende Fenster ausgeführt werden soll.
* *BinSize*: Skalar mit der Größe der Lagerplätze, die über die *IndexColumn*angewendet werden sollen.
* *BinsPerWindow*: Skalar mit der Anzahl der In-Bins, die in jedem Fenster enthalten sind.
* *dim1*, *dim2*, ... : (optional) Liste der Dimensionsspalten, die geschnitten werden sollen.

**Rückgabe**

Gibt eine Tabelle mit einer Zeile pro Lagerplatz (und, falls angegeben) einer Kombination von Dimensionen zurück, die das rollende Perzentil der Werte im Fenster hat, das am Lagerplatz endet (inklusive). unterschiedliche Zählwerte, unterschiedliche Anzahl neuer Werte, aggregierte eindeutige Anzahl für jedes Zeitfenster.

Ausgabetabellenschema ist:


|IndexColumn|dim1|...|dim_n|rolling_BinsPerWindow_percentile_ValueColumn_Pct
|---|---|---|---|---|


**Beispiele**

### <a name="rolling-3-day-median-value-per-day"></a>Rollender 3-Tage-Medianwert pro Tag 

Die nächste Abfrage berechnet einen 3-Tage-Medianwert in täglicher Granularität. Jede Zeile in der Ausgabe stellt den Medianwert für die letzten 3 Abschnitte (Tage) dar, einschließlich des Lagerplatzs selbst.

```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3)
```

|Timestamp|rolling_3_percentile_val_50|
|---|---|
|2018-01-01 00:00:00.0000000|   12|
|2018-01-02 00:00:00.0000000|   24|
|2018-01-03 00:00:00.0000000|   36|
|2018-01-04 00:00:00.0000000|   60|
|2018-01-05 00:00:00.0000000|   84|
|2018-01-06 00:00:00.0000000|   108|
|2018-01-07 00:00:00.0000000|   132|
|2018-01-08 00:00:00.0000000|   156|
|2018-01-09 00:00:00.0000000|   180|
|2018-01-10 00:00:00.0000000|   204|

### <a name="rolling-3-day-median-value-per-day-by-dimension"></a>Rolling 3-Tage-Medianwert pro Tag nach Dimension

Das gleiche Beispiel von oben, aber jetzt auch berechnet das rollende Fenster für jeden Wert der Dimension partitioniert.

```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3, EvenOrOdd)
```

|Timestamp| Evenorodd|  rolling_3_percentile_val_50|
|---|---|---|
|2018-01-01 00:00:00.0000000|   Sogar|   12|
|2018-01-02 00:00:00.0000000|   Sogar|   24|
|2018-01-03 00:00:00.0000000|   Sogar|   36|
|2018-01-04 00:00:00.0000000|   Sogar|   60|
|2018-01-05 00:00:00.0000000|   Sogar|   84|
|2018-01-06 00:00:00.0000000|   Sogar|   108|
|2018-01-07 00:00:00.0000000|   Sogar|   132|
|2018-01-08 00:00:00.0000000|   Sogar|   156|
|2018-01-09 00:00:00.0000000|   Sogar|   180|
|2018-01-10 00:00:00.0000000|   Sogar|   204|
|2018-01-01 00:00:00.0000000|   Ungerade|    11|
|2018-01-02 00:00:00.0000000|   Ungerade|    23|
|2018-01-03 00:00:00.0000000|   Ungerade|    35|
|2018-01-04 00:00:00.0000000|   Ungerade|    59|
|2018-01-05 00:00:00.0000000|   Ungerade|    83|
|2018-01-06 00:00:00.0000000|   Ungerade|    107|
|2018-01-07 00:00:00.0000000|   Ungerade|    131|
|2018-01-08 00:00:00.0000000|   Ungerade|    155|
|2018-01-09 00:00:00.0000000|   Ungerade|    179|
|2018-01-10 00:00:00.0000000|   Ungerade|    203|