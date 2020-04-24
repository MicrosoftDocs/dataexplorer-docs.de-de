---
title: make_set_if() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird make_set_if() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1393e063fb0abb91b38a8b9e1edc0110e78b3638
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512647"
---
# <a name="make_set_if-aggregation-function"></a>make_set_if() (Aggregationsfunktion)

Gibt `dynamic` ein (JSON) Array des Satzes von unterschiedlichen Werten zurück, die *Expr* in der Gruppe aufnimmt, für die *Predikat* ausgewertet wird. `true`

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`summarize``make_set_if(` *Expr*, *Prädikat* [`,` *MaxSize*]`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird.
* *Prädikat*: Prädikat, das `true` ausgewertet werden muss, damit *Expr* dem Ergebnis hinzugefügt wird.
* *MaxSize* ist ein optionaler Ganzzahlgrenzwert für die maximale Anzahl der zurückgegebenen Elemente (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

**Rückgabe**

Gibt `dynamic` ein (JSON) Array des Satzes von unterschiedlichen Werten zurück, die *Expr* in der Gruppe aufnimmt, für die *Predikat* ausgewertet wird. `true`
Die Sortierreihenfolge des Arrays ist nicht definiert.

> [!TIP]
> Um nur die unterschiedlichen Werte zu zählen, verwenden Sie [dcountif()](dcountif-aggfunction.md)

**Siehe auch**

[`make_set`](./makeset-aggfunction.md)Funktion, die dasselbe tut, ohne Prädikatsausdruck.

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
| summarize make_set_if(name, strlen(name) > 4)
```

|set_name|
|----|
|["George", "Ringo"]|