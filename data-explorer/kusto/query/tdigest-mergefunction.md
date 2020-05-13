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
ms.openlocfilehash: 1281e2afdf9770975c6f6f74399f9815adaec045
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83371061"
---
# <a name="tdigest_merge"></a>tdigest_merge()

`tdigest`Führt Ergebnisse aus (skalare Version der Aggregat Version [`tdigest_merge()`](tdigest-merge-aggfunction.md) ).

Weitere Informationen zum zugrunde liegenden Algorithmus (T-Digest) und dem geschätzten Fehler [finden Sie hier](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Syntax**

`merge_tdigests(`*Expr1* `,` *Expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* `, ...)` : Ein Alias.

**Argumente**

* Spalten mit den Werten, die `tdigest` zusammengeführt werden sollen.

**Rückgabe**

Das Ergebnis für das Zusammenführen der Spalten `*Expr1*` , `*Expr2*` ,... `*ExprN*` zu eins `tdigest` .

**Beispiele**

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
