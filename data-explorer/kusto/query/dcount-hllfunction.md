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
ms.openlocfilehash: 2e3847f0ad6c120f076461c5b4774f60349d6125
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348427"
---
# <a name="dcount_hll"></a>dcount_hll()

Berechnet den DCount-Wert aus HLL-Ergebnissen (die von [HLL](hll-aggfunction.md) oder [hll_merge](hll-merge-aggfunction.md)generiert wurden).

Erfahren Sie mehr über den [zugrunde liegenden Algorithmus (*H*yper*L*og*l*OG) und die Schätzung der Schätzung](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Syntax

`dcount_hll(`*Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der von [HLL](hll-aggfunction.md) oder [HLL-Merge](hll-merge-aggfunction.md) generiert wurde.

## <a name="returns"></a>Rückgabe

Die eindeutige Anzahl der einzelnen Werte in *expr*

## <a name="examples"></a>Beispiele

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
