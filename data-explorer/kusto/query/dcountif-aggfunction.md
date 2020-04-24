---
title: dcountif() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt dcountif() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1cc3c17474db835f381cac32d6107acb312c3d11
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516047"
---
# <a name="dcountif-aggregation-function"></a>dcountif() (Aggregationsfunktion)

Gibt eine Schätzung der Anzahl der unterschiedlichen Werte von *Expr* `true`von Zeilen zurück, für die *Predikat* ausgewertet wird. 

* Kann nur im Kontext der Aggregation innerhalb [von summarize](summarizeoperator.md)verwendet werden.

Lesen Sie mehr über die [Schätzgenauigkeit](dcount-aggfunction.md#estimation-accuracy).

**Syntax**

`dcountif(` *Expr*zusammenfassen , *Prädikat*, [`,` *Genauigkeit*]`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird.
* *Prädikat*: Ausdruck, der zum Filtern von Zeilen verwendet wird.
* Mit *Accuracy* wird, sofern angegeben, der Ausgleich zwischen Geschwindigkeit und Genauigkeit gesteuert.
    * `0` ist die am wenigsten präzise und schnellste Berechnung. 1,6% Fehler
    * `1`= der Standardwert, der Genauigkeit und Berechnungszeit ausbalanciert; ca. 0,8% Fehler.
    * `2`= genaue und langsame Berechnung; ca. 0,4% Fehler.
    * `3`= extra genaue und langsame Berechnung; ca. 0,28% Fehler.
    * `4`= super genaue und langsamste Berechnung; ca. 0,2% Fehler.
    
**Rückgabe**

Gibt eine Schätzung der Anzahl der unterschiedlichen Werte von *Expr* `true` von Zeilen zurück, für die *Predikat* in der Gruppe ausgewertet wird. 

**Beispiel**

```kusto
PageViewLog | summarize countries=dcountif(country, country startswith "United") by continent
```

**Tipp: Offset-Fehler**

`dcountif()`kann zu einem einmaligen Fehler in den Edge-Fällen führen, in denen `Predicate` alle Zeilen übergeben werden oder keine der Zeilen, der Ausdruck