---
title: make_list_if () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird make_list_if () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9090e752f018c4abcce759c37a8ecb3571e2fbd6
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102909"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if () (Aggregations Funktion)

Gibt ein- `dynamic` Array (JSON) aller Werte von *expr* in der Gruppe zurück, für die *Predicate* als ausgewertet wird `true` .

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`summarize``make_list_if(` *Expr*, *Prädikat* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird.
* *Predicate*: ein Prädikat, das in ausgewertet `true` werden muss, damit *expr* dem Ergebnis hinzugefügt werden kann.
* *MaxSize* ist eine optionale ganzzahlige Beschränkung für die maximale Anzahl von zurückgegebenen Elementen (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

## <a name="returns"></a>Gibt zurück

Gibt ein- `dynamic` Array (JSON) aller Werte von *expr* in der Gruppe zurück, für die *Predicate* als ausgewertet wird `true` .
Wenn die Eingabe für den `summarize` Operator nicht sortiert ist, ist die Reihenfolge der Elemente im resultierenden Array nicht definiert.
Wenn die Eingabe für den `summarize` Operator sortiert ist, verfolgt die Reihenfolge der Elemente im resultierenden Array die der Eingabe.

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
| summarize make_list_if(name, strlen(name) > 4)
```

|list_name|
|----|
|["George", "Ringo"]|

## <a name="see-also"></a>Weitere Informationen

[`make_list`](./makelist-aggfunction.md) -Funktion, die das gleiche ohne Prädikat Ausdruck verwendet.