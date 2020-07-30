---
title: hll_merge ()-Azure Daten-Explorer
description: In diesem Artikel wird hll_merge () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: ecf16a714b0466a7ffc2da7b69f117383c90970e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347526"
---
# <a name="hll_merge"></a>hll_merge()

`hll`Führt Ergebnisse aus (skalare Version der Aggregat Version [`hll_merge()`](hll-merge-aggfunction.md) ).

Erfahren Sie mehr über den [zugrunde liegenden Algorithmus (*H*yper*L*og*l*OG) und die Schätzung der Schätzung](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Syntax

`hll_merge(`*Expr1* `,` *Expr2*`, ...)`

## <a name="arguments"></a>Argumente

* Spalten `hll` mit Werten, die zusammengeführt werden sollen.

## <a name="returns"></a>Gibt zurück

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
