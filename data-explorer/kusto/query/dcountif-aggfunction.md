---
title: dzähltif () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird DCount () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c14c4d9e0512d4bb054bb3df39acb4828be5a5bd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247539"
---
# <a name="dcountif-aggregation-function"></a>dzähltif () (Aggregations Funktion)

Gibt eine Schätzung der Anzahl von unterschiedlichen *Werten von Zeilen aus,* für die das *Prädikat* ausgewertet wird `true` . 

* Kann nur im Kontext der [Aggregation in zusammen](summarizeoperator.md)Fassung verwendet werden.

Informieren Sie sich über die [Genauigkeit der Schätzung](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Syntax

`dcountif(` *expr*zusammenfassen *, Prädikat*[ `,` *Genauigkeit*]`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird.
* *Predicate*: Ausdruck, der zum Filtern von Zeilen verwendet wird.
* Mit *Accuracy* wird, sofern angegeben, der Ausgleich zwischen Geschwindigkeit und Genauigkeit gesteuert.
    * `0` ist die am wenigsten präzise und schnellste Berechnung. 1,6%-Fehler
    * `1` = der Standardwert, mit dem Genauigkeit und Berechnungszeit ausgeglichen werden. etwa 0,8% Fehler.
    * `2` = exakte und langsame Berechnung; etwa 0,4% Fehler.
    * `3` = Extra genaue und langsame Berechnung; etwa 0,28% Fehler.
    * `4` = sehr präzise und langsamste Berechnung; etwa 0,2% Fehler.
    
## <a name="returns"></a>Rückgabe

Gibt eine Schätzung der Anzahl von unterschiedlichen *Werten von Zeilen aus,*  für die das *Prädikat* `true` in der Gruppe ausgewertet wird. 

## <a name="example"></a>Beispiel

```kusto
PageViewLog | summarize countries=dcountif(country, country startswith "United") by continent
```

**Tipp: Offset Fehler**

`dcountif()`kann in den Edge-Fällen, in denen alle Zeilen bestanden werden, oder keine der Zeilen, den Ausdruck, einen einmaligen Fehler verursachen. `Predicate`