---
title: tdigest_merge() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird tdigest_merge() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 988d7f05791723a823a5850f6865a780477f7bd4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506374"
---
# <a name="tdigest_merge"></a>tdigest_merge()

Führt tdigest-Ergebnisse zusammen (skalare Version [`tdigest_merge()`](tdigest-merge-aggfunction.md)der Aggregatversion ).

Lesen Sie [hier](percentiles-aggfunction.md#estimation-error-in-percentiles)mehr über den zugrunde liegenden Algorithmus (T-Digest) und den geschätzten Fehler .

**Syntax**

`merge_tdigests(`*Expr1* `,` *Expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* `, ...)` - Ein Alias.

**Argumente**

* Spalten, in denen die zu verschmelzenden tdigests enthalten sind.

**Rückgabe**

Das Ergebnis für das `*Expr1*` `*Expr2*`Zusammenführen der Spalten , , ... `*ExprN*` auf einen Tdigest.

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