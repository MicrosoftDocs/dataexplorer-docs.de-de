---
title: dcount_hll() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden dcount_hll() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: a0c921efa90f5d66fe42fa6ee872204b5bb399cd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516149"
---
# <a name="dcount_hll"></a>dcount_hll()

Berechnet die Dcount aus hll-Ergebnissen (die von [hll](hll-aggfunction.md) oder [hll_merge](hll-merge-aggfunction.md)generiert wurde).

Lesen Sie mehr über den [zugrunde liegenden Algorithmus (*H*yper*L*og*L*og) und die Schätzgenauigkeit](dcount-aggfunction.md#estimation-accuracy).

**Syntax**

`dcount_hll(`*Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der durch [hll](hll-aggfunction.md) oder [hll-merge](hll-merge-aggfunction.md) generiert wurde

**Rückgabe**

Die unterschiedliche Anzahl der einzelnen Werte in *Expr*

**Beispiele**

```kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|