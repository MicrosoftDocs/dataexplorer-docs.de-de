---
title: HLL () (Aggregations Funktion)-Azure Daten-Explorer
description: In diesem Artikel wird HLL () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/15/2020
ms.openlocfilehash: f5c47e2ebd2acc0b2ec250d183d65b6536aff756
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741830"
---
# <a name="hll-aggregation-function"></a>HLL () (Aggregations Funktion)

Berechnet die Zwischenergebnisse von [`dcount`](dcount-aggfunction.md) innerhalb der Gruppe, nur im Kontext der Aggregation innerhalb von zusammen [Fassung.](summarizeoperator.md)

Erfahren Sie mehr über den [zugrunde liegenden Algorithmus (*H*yper*L*og*l*OG) und die Schätzung der Schätzung](dcount-aggfunction.md#estimation-accuracy).

**Syntax**

`summarize hll(`*`Expr`* `[,` *`Accuracy`*`])`

**Argumente**

* *`Expr`*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 
* *`Accuracy`* Wenn angegeben, wird das Gleichgewicht zwischen Geschwindigkeit und Genauigkeit gesteuert.

  |Genauigkeits Wert |Genauigkeit  |Geschwindigkeit  |Fehler  |
  |---------|---------|---------|---------|
  |`0` | lowest | schnellste | 1,6% |
  |`1` | default  | Ausgleich | 0,8% |
  |`2` | high | langsam | 0,4%  |
  |`3` | high | langsam | 0,28% |
  |`4` | Extra hoch | langsamste | 0,2% |
    
**Rückgabe**

Die Zwischenergebnisse der unterschiedlichen Anzahl *`Expr`* von in der Gruppe.
 
**Tipps**

1. Mithilfe der Aggregations Funktion [`hll_merge`](hll-merge-aggfunction.md) können Sie mehrere `hll` Zwischenergebnisse zusammenführen (dies funktioniert nur bei `hll` der Ausgabe).

1. Sie können die-Funktion [`dcount_hll`](dcount-hllfunction.md)verwenden, die `dcount` den aus `hll`  /  `hll_merge` Aggregations Funktionen berechnet.

**Beispiele**

```kusto
StormEvents
| summarize hll(DamageProperty) by bin(StartTime,10m)

```

|StartTime|`hll_DamageProperty`|
|---|---|
|2007-09-18 20:00:00.0000000|[[1024, 14], [-5473486921211236216,-6230876016761372746, 3953448761157777955, 4246796580750024372], []]|
|2007-09-20 21:50:00.0000000|[[1024, 14], [4835649640695509390], []]|
|2007-09-29 08:10:00.0000000|[[1024, 14], [4246796580750024372], []]|
|2007-12-30 16:00:00.0000000|[[1024, 14], [4246796580750024372,-8936707700542868125], []]|