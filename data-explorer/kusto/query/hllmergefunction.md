---
title: hll_merge() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden hll_merge() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 10e726af4e9dd2e129b526f016a7c37dc0c99d50
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514092"
---
# <a name="hll_merge"></a>hll_merge()

Führt hll-Ergebnisse zusammen (skalare Version [`hll_merge()`](hll-merge-aggfunction.md)der Aggregatversion ).

Lesen Sie mehr über den [zugrunde liegenden Algorithmus (*H*yper*L*og*L*og) und die Schätzgenauigkeit](dcount-aggfunction.md#estimation-accuracy).

**Syntax**

`hll_merge(`*Expr1* `,` *Expr2*`, ...)`

**Argumente**

* Spalten mit den hll-Werten, die zusammengeführt werden sollen.

**Rückgabe**

Das Ergebnis für das `*Exrp1*` `*Expr2*`Zusammenführen der Spalten , , ... `*ExprN*` auf einen hll-Wert.

**Beispiele**

```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|dcount_hll_merged|
|---|
|20|