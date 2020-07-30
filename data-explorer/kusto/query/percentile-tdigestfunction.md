---
title: percentile_tdigest ()-Azure Daten-Explorer
description: In diesem Artikel wird percentile_tdigest () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 814124dc0ae9fa5f26a198fafc1bf1d7fd2b83e4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346200"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

Berechnet das Perzentil-Ergebnis aus den `tdigest` Ergebnissen (das von [tdigest ()](tdigest-aggfunction.md) oder [tdigest_merge ()](tdigest-merge-aggfunction.md)generiert wurde).

## <a name="syntax"></a>Syntax

`percentile_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *typeliteral*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *Percentile2*]... [ `,` *Percentilen*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*Dynamisches Array*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der von [`tdigest`](tdigest-aggfunction.md) oder [tdigest_merge ()](tdigest-merge-aggfunction.md)generiert wurde.
* *Percentile* ist eine Double-Konstante, die das Quantil angibt.
* *typeliteral*: ein optionales typliteralzeichen (z `typeof(long)` . b.). Wenn bereitgestellt, ist das Resultset von diesem Typ. 
* *Dynamisches Array*: Liste der Perzentile in einem dynamischen Array von ganzzahligen oder Gleit Komma Zahlen.

## <a name="returns"></a>Rückgabe

Der Quantile/percentilesw-Wert jedes Werts in *`Expr`* .

**Tipps**

* Die Funktion muss mindestens einen Prozentsatz erhalten (und vielleicht auch weitere Informationen finden Sie in der obigen Syntax: *Percentile1* [ `,` *Percentile2*]... [ `,` *Percentilen*]), und das Ergebnis ist ein dynamisches Array, das die Ergebnisse enthält. (z. b. [`percentiles()`](percentiles-aggfunction.md) )
  
* Wenn nur ein Prozentsatz angegeben wurde und der Typ ebenfalls bereitgestellt wurde, ist das Ergebnis eine Spalte desselben Typs, die mit den Ergebnissen dieses Prozentsatzes bereitgestellt wird. In diesem Fall müssen alle `tdigest` Funktionen diesen Typ aufweisen.

* Wenn *`Expr`* `tdigest` Funktionen verschiedener Typen enthält, geben Sie den-Typ nicht an. Das Ergebnis ist vom Typ "Dynamic". Hier einige Beispiele.

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentile_tdigest(tdigestRes, 100, typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|0|
|62 Millionen|
|110 Millionen|
|1,2 Millionen|
|250.000|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentiles_array_tdigest(tdigestRes, range(0, 100, 50), typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|[0, 0, 0]|
|[0, 0, 62000000]|
|[0, 0, 110000000]|
|[0, 0, 1200000]|
|[0,0]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| union (StormEvents | summarize tdigestRes = tdigest(EndTime) by State)
| project percentile_tdigest(tdigestRes, 100)
```

|percentile_tdigest_tdigestRes|
|---|
|[0]|
|[62 Millionen]|
|["2007-12-20t11:30:00.0000000 z"]|
|["2007-12-31t23:59:00.0000000 z"]|
