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
ms.openlocfilehash: 262676e2ee40b619c4984a23c23818c493aad47a
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103058"
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

## <a name="returns"></a>Gibt zurück

Gibt ein- `dynamic` Array (JSON) des Satzes unterschiedlicher Werte zurück, die von *expr* in der Gruppe berücksichtigt werden, für die das *Prädikat* als ausgewertet wird `true` .
Die Sortierreihenfolge des Arrays ist nicht definiert.

> [!TIP]
> Um nur die unterschiedlichen Werte zu zählen, verwenden Sie [dantif ()](dcountif-aggfunction.md) .

## <a name="see-also"></a>Weitere Informationen

[`make_set`](./makeset-aggfunction.md) -Funktion, die das gleiche ohne Prädikat Ausdruck verwendet.

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