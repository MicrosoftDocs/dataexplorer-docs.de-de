---
title: sumif() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird sumif() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7d2c96f73b404e8d9acbe9da9defecd6bf1bbf3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506663"
---
# <a name="sumif-aggregation-function"></a>sumif() (Aggregationsfunktion)

Gibt eine Summe von *Expr* zurück, für `true`die *Predikat* ausgewertet wird.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

Sie können auch die [Sum()-Funktion](sum-aggfunction.md) verwenden, die Zeilen ohne Prädikatsausdruck summiert.

**Syntax**

`sumif(` *Expr*`,`*Predikat* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck für die Aggregationsberechnung. 
* *Prädikat*: Prädikat, dass, wenn true, der berechnete Wert des *Expr*zur Summe addiert wird. 

**Rückgabe**

Der Summenwert von *Expr,* für den `true` *Predikat* ausgewertet wird, zu .

> [!TIP]
> Verwenden von `summarize sumif(expr, filter)` anstelle von `where filter | summarize sum(expr)`

**Beispiel**

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize sumif(day_of_birth, strlen(name) > 4)
```

|sumif_day_of_birth|
|----|
|32|