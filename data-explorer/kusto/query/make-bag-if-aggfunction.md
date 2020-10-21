---
title: make_bag_if () (Aggregations Funktion)-Azure Daten-Explorer
description: In diesem Artikel wird make_bag_if () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 997ba519016bf6f6774af3f305ab78515c36c4ed
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249926"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if () (Aggregations Funktion)

Gibt ein `dynamic` (JSON)-Eigenschaften Behälter (Wörterbuch) aller Werte von *' expr '* in der Gruppe zurück, für die das *Prädikat* ergibt `true` .

> [!NOTE]
> Kann nur im Kontext der [Aggregation in zusammen](summarizeoperator.md)Fassung verwendet werden.

## <a name="syntax"></a>Syntax

`summarize``make_bag_if(` *Expr*, *Prädikat* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Argumente

* *Expr*: ein Ausdruck vom Typ `dynamic` , der für die Aggregations Berechnung verwendet wird.
* *Predicate*: ein Prädikat, das für ausgewertet `true` werden muss, damit *' expr '* dem Ergebnis hinzugefügt werden kann.
* *MaxSize*: ein optionales ganzzahliges Limit für die maximale Anzahl von zurückgegebenen Elementen (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

## <a name="returns"></a>Rückgabe

Gibt ein `dynamic` (JSON)-Eigenschaften Behälter (Wörterbuch) aller Werte von *"expr"* in der Gruppe zurück, die Eigenschaften Behälter (Wörterbücher) sind, für die das *Prädikat* ausgewertet wird `true` .
Nicht-Wörterbuch Werte werden übersprungen.
Wenn ein Schlüssel in mehr als einer Zeile angezeigt wird, wird ein beliebiger Wert aus den möglichen Werten für diesen Schlüssel ausgewählt.

> [!NOTE]
> Die- [`make_bag`](./make-bag-aggfunction.md) Funktion ähnelt make_bag_if () ohne Prädikat Ausdruck.

## <a name="examples"></a>Beispiele

```kusto
let T = datatable(prop:string, value:string, predicate:bool)
[
    "prop01", "val_a", true,
    "prop02", "val_b", false,
    "prop03", "val_c", true
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag_if(p, predicate)

```

|dict|
|----|
|{"prop01": "val_a", "prop03": "val_c"} |

Verwenden Sie [bag_unpack ()](bag-unpackplugin.md) -Plug-in, um die Behälter Schlüssel in der make_bag_if ()-Ausgabe in Spalten umzuwandeln. 

```kusto
let T = datatable(prop:string, value:string, predicate:bool)
[
    "prop01", "val_a", true,
    "prop02", "val_b", false,
    "prop03", "val_c", true
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag_if(p, predicate)
| evaluate bag_unpack(bag)

```

|prop01|prop03|
|---|---|
|val_a|val_c|
