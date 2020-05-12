---
title: Case ()-Azure-Daten-Explorer
description: In diesem Artikel wird Case () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b493f74472454649a557b7e3677b26af169413de
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227552"
---
# <a name="case"></a>case()

Wertet eine Liste von Prädikaten aus und gibt den ersten Ergebnis Ausdruck zurück, dessen Prädikat erfüllt ist.

Wenn keines der Prädikate zurückgibt `true` , wird das Ergebnis des letzten Ausdrucks (der `else` ) zurückgegeben.
Alle ungeraden Argumente (Anzahl Starts bei 1) müssen Ausdrücke sein, die zu einem Wert ausgewertet werden `boolean` .
Alle geraden Argumente (die `then` s) und das letzte Argument (der `else` ) müssen denselben Typ aufweisen.

**Syntax**

`case(`*predicate_1* `,` *then_1*, *predicate_2* `,` *then_2*, *predicate_3* `,` *then_3*. *else*`)`

**Argumente**

* *predicate_i*: ein Ausdruck, der zu einem `boolean` Wert ausgewertet wird.
* *then_i*: ein Ausdruck, der ausgewertet wird und dessen Wert von der Funktion zurückgegeben wird, wenn *predicate_i* das erste Prädikat ist, das ergibt `true` .
* *else*: ein Ausdruck, der ausgewertet wird und dessen Wert von der Funktion zurückgegeben wird, wenn keine der *predicate_i* ausgewertet wird `true` .

**Rückgabe**

Der Wert des ersten *then_i* , dessen *predicate_i* als ausgewertet `true` wird, oder der Wert von *else* , wenn keine der Prädikate erfüllt ist.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range Size from 1 to 15 step 2
| extend bucket = case(Size <= 3, "Small", 
                       Size <= 10, "Medium", 
                       "Large")
```

|Size|bucket|
|---|---|
|1|Klein|
|3|Klein|
|5|Medium (Mittel)|
|7|Medium (Mittel)|
|9|Medium (Mittel)|
|11|Groß|
|13|Groß|
|15|Groß|
