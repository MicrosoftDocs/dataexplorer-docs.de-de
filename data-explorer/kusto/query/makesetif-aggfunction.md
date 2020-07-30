---
title: make_set_if () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird make_set_if () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8d6b26a13539d88aae57774cc35cf57d321b67f4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346897"
---
# <a name="make_set_if-aggregation-function"></a>make_set_if () (Aggregations Funktion)

Gibt ein- `dynamic` Array (JSON) des Satzes unterschiedlicher Werte zurück, die von *expr* in der Gruppe berücksichtigt werden, für die das *Prädikat* als ausgewertet wird `true` .

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`summarize``make_set_if(` *Expr*, *Prädikat* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird.
* *Predicate*: das Prädikat, das ausgewertet werden muss, damit `true` *expr* dem Ergebnis hinzugefügt wird.
* *MaxSize* ist eine optionale ganzzahlige Beschränkung für die maximale Anzahl von zurückgegebenen Elementen (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

## <a name="returns"></a>Rückgabe

Gibt ein- `dynamic` Array (JSON) des Satzes unterschiedlicher Werte zurück, die von *expr* in der Gruppe berücksichtigt werden, für die das *Prädikat* als ausgewertet wird `true` .
Die Sortierreihenfolge des Arrays ist nicht definiert.

> [!TIP]
> Um nur die unterschiedlichen Werte zu zählen, verwenden Sie [dantif ()](dcountif-aggfunction.md) .

**Weitere Informationen**

[`make_set`](./makeset-aggfunction.md)-Funktion, die das gleiche ohne Prädikat Ausdruck verwendet.

## <a name="example"></a>Beispiel

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