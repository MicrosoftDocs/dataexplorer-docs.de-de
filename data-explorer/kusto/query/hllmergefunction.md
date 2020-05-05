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
ms.openlocfilehash: 35dab83a658b714a220c7fbd6ff751627c0741e4
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741787"
---
# <a name="hll_merge"></a>hll_merge()

`hll` Führt Ergebnisse aus (skalare Version der Aggregat Version [`hll_merge()`](hll-merge-aggfunction.md)).

Erfahren Sie mehr über den [zugrunde liegenden Algorithmus (*H*yper*L*og*l*OG) und die Schätzung der Schätzung](dcount-aggfunction.md#estimation-accuracy).

**Syntax**

`hll_merge(`*Expr1* `,` *expr2*`, ...)`

**Argumente**

* Spalten mit `hll` Werten, die zusammengeführt werden sollen.

**Rückgabe**

Das Ergebnis für das Zusammenführen `*Exrp1*`der `*Expr2*`Spalten,,... `*ExprN*` zu einem `hll` Wert.

**Beispiele**

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