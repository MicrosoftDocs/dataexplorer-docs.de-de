---
title: make_list_if() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird make_list_if() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b34c1dad7be709145c622c97b357734c25292bba
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512732"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if() (Aggregationsfunktion)

Gibt `dynamic` ein Array (JSON) aller Werte von *Expr* in der Gruppe `true`zurück, für die *Predikat* ausgewertet wird.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`summarize``make_list_if(` *Expr*, *Prädikat* [`,` *MaxSize*]`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird.
* *Prädikat*: Prädikat, das `true`auf ausgewertet werden muss, damit *Expr* dem Ergebnis hinzugefügt werden kann.
* *MaxSize* ist ein optionaler Ganzzahlgrenzwert für die maximale Anzahl der zurückgegebenen Elemente (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

**Rückgabe**

Gibt `dynamic` ein Array (JSON) aller Werte von *Expr* in der Gruppe `true`zurück, für die *Predikat* ausgewertet wird.
Wenn die Eingabe `summarize` für den Operator nicht sortiert ist, ist die Reihenfolge der Elemente im resultierenden Array nicht definiert.
Wenn die Eingabe `summarize` für den Operator sortiert ist, wird die Reihenfolge der Elemente im resultierenden Array die Reihenfolge der Eingabe nachverfolgt.

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
| summarize make_list_if(name, strlen(name) > 4)
```

|list_name|
|----|
|["George", "Ringo"]|

**Siehe auch**

[`make_list`](./makelist-aggfunction.md)Funktion, die dasselbe tut, ohne Prädikatsausdruck.