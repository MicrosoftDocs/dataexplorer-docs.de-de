---
title: 'rolling_percentile-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird rolling_percentile-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f64a7e5c183e34e81781986d5c28f189a04291e7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242879"
---
# <a name="rolling_percentile-plugin"></a>rolling_percentile ()-Plug-in

Gibt eine Schätzung für das angegebene Perzentil der *valuecolren* -Population in einem parallelen (gleitenden) *binsperwindow* size-Fenster pro *binsize*zurück.

```kusto
T | evaluate rolling_percentile(ValueColumn, Percentile, IndexColumn, BinSize, BinsPerWindow)
```

## <a name="syntax"></a>Syntax

*T* `| evaluate` `rolling_percentile(` *valuecolenn* `,` *Percentile* `,` *indexcolumn* `,` *Binär Größe* `,` *binsperwindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>Argumente

* *T*: der tabellarische Eingabe Ausdruck.
* *Valuecolenumn*: der Name der Spalte mit Werten, deren Quantil berechnet werden soll. 
* *Perzentil*: Skalar mit dem zu berechnenden Quantil.
* *Indexcolumn*: der Name der Spalte, über die das parallele Fenster ausgeführt werden soll.
* *Binsize*: Skalar mit der Größe der Container, die über die *indexcolumn*angewendet werden sollen.
* *Binsperwindow*: Skalar mit der Anzahl der in jedem Fenster enthaltenen Behälter.
* *dim1*, *dim2*,...: (optional) Liste der Dimensions Spalten, nach denen die Segmente aufgeteilt werden sollen.

## <a name="returns"></a>Rückgabe

Gibt eine Tabelle mit einer Zeile für jede einzelne bin (und ggf. eine Kombination von Dimensionen) zurück, die das parallele Quantil der Werte im Fenster enthält, das auf den bin (inklusiv) endet. unterschiedliche Anzahl Werte, unterschiedliche Anzahl neuer Werte, aggregierte eindeutige Anzahl für jedes Zeitfenster.

Das Ausgabe Tabellen Schema ist:


|Indexcolumn|dim1|...|dim_n|rolling_BinsPerWindow_percentile_ValueColumn_Pct
|---|---|---|---|---|


## <a name="examples"></a>Beispiele

### <a name="rolling-3-day-median-value-per-day"></a>Paralleler 3-Tage-Medianwert pro Tag 

Die nächste Abfrage berechnet einen 3-tägigen Medianwert in täglicher Granularität. Jede Zeile in der Ausgabe stellt den Medianwert für die letzten drei Behälter (Tage) dar, einschließlich der bin selbst.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

### <a name="rolling-3-day-median-value-per-day-by-dimension"></a>Paralleler 3-Tage-Medianwert pro Tag nach Dimension

Das gleiche Beispiel oben, aber jetzt auch das parallele Fenster berechnet, das für jeden Wert der Dimension partitioniert ist.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|2018-01-01 00:00:00.0000000|   Gelegentlich|    11|
|2018-01-02 00:00:00.0000000|   Gelegentlich|    23|
|2018-01-03 00:00:00.0000000|   Gelegentlich|    35|
|2018-01-04 00:00:00.0000000|   Gelegentlich|    59|
|2018-01-05 00:00:00.0000000|   Gelegentlich|    83|
|2018-01-06 00:00:00.0000000|   Gelegentlich|    107|
|2018-01-07 00:00:00.0000000|   Gelegentlich|    131|
|2018-01-08 00:00:00.0000000|   Gelegentlich|    155|
|2018-01-09 00:00:00.0000000|   Gelegentlich|    179|
|2018-01-10 00:00:00.0000000|   Gelegentlich|    203|
