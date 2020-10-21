---
title: hll_merge ()-Azure Daten-Explorer
description: In diesem Artikel wird hll_merge () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 6bf364106bef8fbe626f96c22502dae748884180
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252975"
---
# <a name="hll_merge"></a>hll_merge()

`hll`Führt Ergebnisse aus (skalare Version der Aggregat Version [`hll_merge()`](hll-merge-aggfunction.md) ).

Erfahren Sie mehr über den [zugrunde liegenden Algorithmus (*H*yper*L*og*l*OG) und die Schätzung der Schätzung](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Syntax

`hll_merge(`*Expr1* `,` *Expr2*`, ...)`

## <a name="arguments"></a>Argumente

* Spalten `hll` mit Werten, die zusammengeführt werden sollen.

## <a name="returns"></a>Rückgabe

Das Ergebnis für das Zusammenführen der Spalten `*Exrp1*` , `*Expr2*` ,... `*ExprN*` zu einem `hll` Wert.

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|`dcount_hll_merged`|
|---|
|20|
