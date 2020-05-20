---
title: dcount_hll ()-Azure Daten-Explorer
description: In diesem Artikel wird dcount_hll () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 1b1b0c2313f32044a7988e0992c00786885ce2aa
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550298"
---
# <a name="dcount_hll"></a>dcount_hll()

Berechnet den DCount-Wert aus HLL-Ergebnissen (die von [HLL](hll-aggfunction.md) oder [hll_merge](hll-merge-aggfunction.md)generiert wurden).

Erfahren Sie mehr über den [zugrunde liegenden Algorithmus (*H*yper*L*og*l*OG) und die Schätzung der Schätzung](dcount-aggfunction.md#estimation-accuracy).

**Syntax**

`dcount_hll(`*Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der von [HLL](hll-aggfunction.md) oder [HLL-Merge](hll-merge-aggfunction.md) generiert wurde.

**Rückgabe**

Die eindeutige Anzahl der einzelnen Werte in *expr*

**Beispiele**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|
