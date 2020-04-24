---
title: hll() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird hll() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/15/2020
ms.openlocfilehash: 52eac2984ed29bf8de21fb378a84b789015aa1c5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514126"
---
# <a name="hll-aggregation-function"></a>hll() (Aggregationsfunktion)

Berechnet die Zwischenergebnisse der Anzahl der [Werte](dcount-aggfunction.md) in der Gruppe. 

* Kann nur im Kontext der Aggregation innerhalb [von summarize](summarizeoperator.md)verwendet werden.

Lesen Sie mehr über den [zugrunde liegenden Algorithmus (*H*yper*L*og*L*og) und die Schätzgenauigkeit](dcount-aggfunction.md#estimation-accuracy).

**Syntax**

`summarize``hll(` *Expr* `,` [ *Genauigkeit*]`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 
* Mit *Accuracy* wird, sofern angegeben, der Ausgleich zwischen Geschwindigkeit und Genauigkeit gesteuert.
    * `0` ist die am wenigsten präzise und schnellste Berechnung. 1,6% Fehler
    * `1`= der Standardwert, der Genauigkeit und Berechnungszeit ausbalanciert; ca. 0,8% Fehler.
    * `2`= genaue und langsame Berechnung; ca. 0,4% Fehler.
    * `3`= extra genaue und langsame Berechnung; ca. 0,28% Fehler.
    * `4`= super genaue und langsamste Berechnung; ca. 0,2% Fehler.
    
**Rückgabe**

Die Zwischenergebnisse der unterschiedlichen Anzahl von *Expr* in der gesamten Gruppe.
 
**Tipps**

1) Sie können die Aggregationsfunktion [hll_merge](hll-merge-aggfunction.md) verwenden, um mehr als ein hll Zwischenergebnis zusammenzuführen (es funktioniert nur bei der hll-Ausgabe).

2) Sie können die Funktion [dcount_hll](dcount-hllfunction.md) verwenden, die die dcount aus hll / hll_merge Aggregationsfunktionen berechnet.

**Beispiele**

```kusto
StormEvents
| summarize hll(DamageProperty) by bin(StartTime,10m)

```

|StartTime|hll_DamageProperty|
|---|---|
|2007-09-18 20:00:00.0000000|[[1024,14],[-5473486921211236216,-6230876016761372746,3953448761157777955,4246796580750024372],[]]|
|2007-09-20 21:50:00.0000000|[[1024,14],[4835649640695509390],[]]|
|2007-09-29 08:10:00.0000000|[[1024,14],[4246796580750024372],[]]|
|2007-12-30 16:00:00.0000000|[[1024,14],[4246796580750024372,-8936707700542868125],[]]|