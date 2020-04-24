---
title: case() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird case() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 479c4a99d410a2df7a608531914d7dccfc555e7e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517271"
---
# <a name="case"></a>case()

Bewertet eine Liste von Prädikaten und gibt den ersten Ergebnisausdruck zurück, dessen Prädikat erfüllt ist.

Wenn keiner der Prädikate `true`zurückkehrt, wird das Ergebnis `else`des letzten Ausdrucks (der ) zurückgegeben.
Alle ungeraden Argumente (Anzahl beginnt bei 1) müssen Ausdrücke sein, die zu einem `boolean` Wert ausgewertet werden.
Alle geraden `then`Argumente (das s) `else`und das letzte Argument (das ) müssen vom gleichen Typ sein.

**Syntax**

`case(`*predicate_1* `,` *then_1*, *predicate_2* `,` then_2 *else* *then_2* *, predicate_3* `,` *then_3*`)`

**Argumente**

* *predicate_i*: Ein Ausdruck, `boolean` der zu einem Wert ausgewertet wird.
* *then_i*: Ein Ausdruck, der ausgewertet wird, und sein Wert wird von der `true`Funktion *zurückgegeben,* wenn predicate_i das erste Prädikat ist, das zu ausgewertet wird.
* *else*: Ein Ausdruck, der ausgewertet wird, und sein *predicate_i* Wert `true`wird von der Funktion zurückgegeben, wenn keine der predicate_i in ausgewertet.

**Rückgabe**

Der Wert der ersten *then_i* deren *predicate_i* auf `true`ausgewertet wird, oder der Wert von *anderen,* wenn keines der Prädikate erfüllt ist.

**Beispiel**

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
|5|Medium|
|7|Medium|
|9|Medium|
|11|Groß|
|13|Groß|
|15|Groß|
