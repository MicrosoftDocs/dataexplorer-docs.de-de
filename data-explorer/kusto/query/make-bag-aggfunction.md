---
title: make_bag () (Aggregations Funktion)-Azure Daten-Explorer
description: In diesem Artikel wird die make_bag ()-Aggregations Funktion in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 18d0bf2eaa0f5215e38b8b787178ea5934fb3737
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347033"
---
# <a name="make_bag-aggregation-function"></a>make_bag () (Aggregations Funktion)

Gibt ein `dynamic` (JSON)-Eigenschaften Behälter (Wörterbuch) aller Werte von *`Expr`* in der Gruppe zurück.

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`summarize``make_bag(` *`Expr`* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Argumente

* *Expr*: ein Ausdruck vom Typ `dynamic` , der für Aggregations Berechnungen verwendet wird.
* *MaxSize* ist eine optionale ganzzahlige Beschränkung für die maximale Anzahl der zurückgegebenen Elemente. Der Standardwert ist *1048576*. Der MaxSize-Wert darf *1048576*nicht überschreiten.

**Hinweis**

Eine Legacy-und veraltete Variante der Funktion `make_dictionary()` hat ein Standard Limit von *MaxSize* = 128.

## <a name="returns"></a>Rückgabe

Gibt ein `dynamic` (JSON)-Eigenschaften Behälter (Wörterbuch) aller Werte von *`Expr`* in der Gruppe zurück, bei denen es sich um Eigenschaften Behälter handelt.
Nicht-Wörterbuch Werte werden übersprungen.
Wenn ein Schlüssel in mehr als einer Zeile angezeigt wird, wird ein beliebiger Wert aus den möglichen Werten für diesen Schlüssel ausgewählt.

**Weitere Informationen**

Verwenden Sie das [bag_unpack ()](bag-unpackplugin.md) -Plug-in zum Erweitern dynamischer JSON-Objekte in Spalten, die Eigenschaften Behälter Schlüssel verwenden. 

## <a name="examples"></a>Beispiele

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag(p)

```

|dict|
|----|
|{"prop01": "val_a", "prop02": "val_b", "prop03": "val_c"} |

Verwenden Sie das [bag_unpack ()](bag-unpackplugin.md) -Plug-in, um die Behälter Schlüssel in der make_bag ()-Ausgabe in Spalten umzuwandeln. 

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag(p)
| evaluate bag_unpack(bag) 

```

|prop01|prop02|prop03|
|---|---|---|
|val_a|val_b|val_c|
