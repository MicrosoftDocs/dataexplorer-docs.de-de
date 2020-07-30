---
title: tdigest_merge ()-Azure Daten-Explorer
description: In diesem Artikel wird tdigest_merge () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: f85c2c45ff4e69ba59f2a13313c8c2ac494c56a6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340988"
---
# <a name="tdigest_merge"></a>tdigest_merge()

`tdigest`Führt Ergebnisse aus (skalare Version der Aggregat Version [`tdigest_merge()`](tdigest-merge-aggfunction.md) ).

Weitere Informationen zum zugrunde liegenden Algorithmus (T-Digest) und dem geschätzten Fehler [finden Sie hier](percentiles-aggfunction.md#estimation-error-in-percentiles).

## <a name="syntax"></a>Syntax

`merge_tdigests(`*Expr1* `,` *Expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* `, ...)` : Ein Alias.

## <a name="arguments"></a>Argumente

* Spalten mit den Werten, die `tdigest` zusammengeführt werden sollen.

## <a name="returns"></a>Rückgabe

Das Ergebnis für das Zusammenführen der Spalten `*Expr1*` , `*Expr2*` ,... `*ExprN*` zu eins `tdigest` .

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize tdigestX = tdigest(x), tdigestY = tdigest(y)
| project merged = tdigest_merge(tdigestX, tdigestY)
| project percentile_tdigest(merged, 100, typeof(long))
```

|percentile_tdigest_merged|
|---|
|20|
