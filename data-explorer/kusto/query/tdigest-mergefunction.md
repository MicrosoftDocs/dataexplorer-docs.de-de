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
ms.openlocfilehash: 92dce1a98cc0e24dcfbfcd7cb875fa370e3ae1d0
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737724"
---
# <a name="tdigest_merge"></a>tdigest_merge()

`tdigest` Führt Ergebnisse aus (skalare Version der Aggregat Version [`tdigest_merge()`](tdigest-merge-aggfunction.md)).

Weitere Informationen zum zugrunde liegenden Algorithmus (T-Digest) und dem geschätzten Fehler [finden Sie hier](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Syntax**

`merge_tdigests(`*Expr1* `,` *expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* expr2`, ...)` -Alias.

**Argumente**

* Spalten mit den Werten `tdigest` , die zusammengeführt werden sollen.

**Rückgabe**

Das Ergebnis für das Zusammenführen `*Expr1*`der `*Expr2*`Spalten,,... `*ExprN*` zu eins `tdigest`.

**Beispiele**

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