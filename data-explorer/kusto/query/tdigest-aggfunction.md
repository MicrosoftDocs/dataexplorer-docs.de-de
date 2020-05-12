---
title: tdigest () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird tdigest () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 1a44945a22d08a1a5a55a5fc130f7ca4247f1fb8
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224985"
---
# <a name="tdigest-aggregation-function"></a>tdigest () (Aggregations Funktion)

Berechnet die Zwischenergebnisse von [`percentiles()`](percentiles-aggfunction.md) in der Gruppe. 

* Kann nur im Kontext der [Aggregation in zusammen](summarizeoperator.md)Fassung verwendet werden.

Erfahren Sie mehr über den [zugrunde liegenden Algorithmus (T-Digest) und den geschätzten Fehler](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Syntax**

`summarize``tdigest(` *Expr* [ `,` *gewitexpr*]`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 
* *Weightexpr*: Ausdruck, der als Gewichtung von Werten für die Aggregations Berechnung verwendet wird.

    
**Rückgabe**

Die Zwischenergebnisse gewichteter Perzentilen von *expr* in der Gruppe.
 
 
**Tipps**

1) Sie können die Aggregations Funktion [tdigest_merge ()](tdigest-merge-aggfunction.md) verwenden, um die Ausgabe von tdigest erneut in einer anderen Gruppe zusammenzuführen.

2) Sie können die-Funktion [percentile_tdigest ()](percentile-tdigestfunction.md) verwenden, um das Quantil/Percentil der tdigest-Ergebnisse zu berechnen.

**Beispiele**

```kusto
StormEvents
| summarize tdigest(DamageProperty) by State
```

|Bundesland/Kanton|tdigest_DamageProperty|
|---|---|
|Atlantik, Süden|[[5], [0], [193]]|
|Flori|[[5], [250, 10, 600000, 5.000, 375000, 15000000, 20000, 6000000, 0, 110000, 150000, 500, 12.000, 30.000, 15000, 46000000, 7000000, 6200000, 200.000, 40000, 8000, 52000000, 62000000, 1200000, 130000, 1500000, 4000000, 7000, 250.000, 875000, 3000, 100000, 10600000, 300.000, 1000000, 25000, 75000, 2000, 60000, 10000, 170000, 350000, 50000, 1000, 16000, 80.000, 2.500, 400000], [9, 1, 1, 22, 1, 1, 9, 1842, 1, 3, 7, 2, 4, 7, 1, 1, 1, 2, 5, 3, 3, 1, 1, 1, 1, 2, 2, 1, 1, 9, 7, 1, 1, 2, 5, 2, 9, 2, 27, 1, 1, 7, 27, 1, 1, 1]]|
|Georgien|[[5], [468, 209, 300.000, 3000, 250.000, 775000, 14000, 500.000, 0, 75000, 4500000, 500, 6928, 22767, 9714, 800000, 700000, 600000, 150.000, 25000, 5.000, 1600000, 1250000, 2700000, 1500000, 2250000, 400000, 4000, 175000, 325000, 2.500, 73750, 750000, 1400000, 350000, 28000000, 39000, 1.500, 35000, 6455, 140000, 225000, 30000, 1000, 110000000, 21700000, 2000, 275000, 200.000, 100000, 1000000, 2600000, 370000, 2100000, 355000, 117500, 50000, 20100, 10000], [11, 11, 4, 53, 21, 1, 6, 10, 1317, 8, 1, 56, 8, 6, 7, 1, 1, 1, 14, 29, 69, 1, 2, 1, 1, 1, 3, 14, 5, 1, 3, 4, 4, 1, 4, 1, 5, 14, 3, 5, 2, 1, 9, 96, 1, 1, 72, 1, 10, 17, 3, 1, 1, 1, 1, 2, 21, 4, 31]]|
|Mississippi|[[5], [267, 55, 90000, 3000, 75000, 300000, 11167, 160000, 0, 32000, 40000, 1000, 7000, 13000, 8000, 400000, 200.000, Wert 180000, 50000, 15000, 5.000, 700000, 500.000, 120000, 650000, 1000000, 150.000, 4000, 60000, 100000, 2.500, 30000, 250.000, 600000, 110000, 12000, 20000, 1.500, 17000, 6000, 45000, 70.000, 15250, 1219, 10000, 25000, 2.000, 80000, 65.000, 35000, 450000, 1200000, 130000, 750000], [3, 2, 6, 21, 1, 4, 6, 1741, 4, 13, 44, 8, 2, 8, 1, 5, 1, 23, 21, 32, 1, 3, 1, 1, 1, 5, 18, 17, 4, 1, 14, 2, 4, 4, 16, 13, 10, 4, 9, 2, 10, 4, 8, 31, 17, 51, 13, 1, 1, 1, 2, 1, 1]]|
|Amerikanisches Samoa|[[5], [0, 250.000], [15, 1]]|