---
title: percentile_tdigest() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird percentile_tdigest() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 655a0693b8e04b1230f6b9e8fe247bd2d77b7ac6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511236"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

Berechnet das Perzentilergebnis aus tdigest-Ergebnissen (die von [tdigest()](tdigest-aggfunction.md) oder [tdigest_merge()](tdigest-merge-aggfunction.md)generiert wurden) )

**Syntax**

`percentile_tdigest(`*Expr* `,` *Perzentil1* [`,` *typLiteral*]`)`

`percentiles_array_tdigest(`*Expr* `,` *Perzentil1* [`,` *Perzentil2*] ... [`,` *PerzentilN*]`)`

`percentiles_array_tdigest(`*Expr* `,` *Dynamic-Array*`)`

**Argumente**

* *Expr*: Ausdruck, der durch [tdigest](tdigest-aggfunction.md) oder [tdigest_merge()](tdigest-merge-aggfunction.md)erzeugt wurde.
* *Perzentil* ist eine doppelte Konstante, die das Perzentil angibt.
* *typeLiteral*: Ein optionales Typliteral `typeof(long)`(z. B. ). Wenn angegeben, wird das Resultset von diesem Typ sein. 
* *Dynamisches Array*: Liste der Perzentile in einem dynamischen Array von Ganzzahl- oder Gleitkommazahlen

**Rückgabe**

Der Perzentil/Perzentilsw-Wert jedes Wertes in *Expr*.

**Tipps**

1) Die Funktion muss mindestens ein Prozent erhalten (und vielleicht noch mehr, siehe die obige Syntax: *Perzentil1* [`,` *Perzentil2*] ... [`,` *PerzentilN*]) und das Ergebnis wird ein dynamisches Array sein, das die Ergebnisse enthält. (wie [`percentiles()`](percentiles-aggfunction.md))
  
2) Wenn nur ein Prozent angegeben wurde und der Typ auch angegeben wurde, dann ist das Ergebnis eine Spalte desselben Typs, die mit den Ergebnissen dieses Prozentsatzes versehen ist (in diesem Fall müssen alle tdigests von diesem Typ sein).

3) Wenn *Expr* Tdigests verschiedener Typen enthält, geben Sie den Typ nicht an, und das Ergebnis ist vom Typ dynamisch. (siehe Beispiele unten).

**Beispiele**

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentile_tdigest(tdigestRes, 100, typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|0|
|62000000|
|110000000|
|1200000|
|250.000|


```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentiles_array_tdigest(tdigestRes, range(0, 100, 50), typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|[0,0,0]|
|[0,0,62000000]|
|[0,0,110000000]|
|[0,0,1200000]|
|[0,0,250000]|


```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| union (StormEvents | summarize tdigestRes = tdigest(EndTime) by State)
| project percentile_tdigest(tdigestRes, 100)
```

|percentile_tdigest_tdigestRes|
|---|
|[0]|
|[62000000]|
|["2007-12-20T11:30:00.0000000Z"]|
|["2007-12-31T23:59:00.0000000Z"]|