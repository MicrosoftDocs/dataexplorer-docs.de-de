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
ms.openlocfilehash: 2cddd645b0b89b3356adabae26874f93b41f8815
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226549"
---
# <a name="hll_merge"></a>hll_merge()

`hll`Führt Ergebnisse aus (skalare Version der Aggregat Version [`hll_merge()`](hll-merge-aggfunction.md) ).

Erfahren Sie mehr über den [zugrunde liegenden Algorithmus (*H*yper*L*og*l*OG) und die Schätzung der Schätzung](dcount-aggfunction.md#estimation-accuracy).

**Syntax**

`hll_merge(`*Expr1* `,` *Expr2*`, ...)`

**Argumente**

* Spalten `hll` mit Werten, die zusammengeführt werden sollen.

**Rückgabe**

Das Ergebnis für das Zusammenführen der Spalten `*Exrp1*` , `*Expr2*` ,... `*ExprN*` zu einem `hll` Wert.

**Beispiele**

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
