---
title: make_list () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird make_list () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: bd923724a391cfc1638057fec654a1bd74b78479
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303359"
---
# <a name="make_list-aggregation-function"></a>make_list () (Aggregations Funktion)

Gibt ein `dynamic` -Array (JSON) aller Werte von *Expr* in der Gruppe zurück.

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`summarize``make_list(` *Expr* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird.
* *MaxSize* ist eine optionale ganzzahlige Beschränkung für die maximale Anzahl von zurückgegebenen Elementen (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

> [!NOTE]
> `makelist()` ist eine veraltete und veraltete Version der `make_list` Funktion. Die Legacy Version hat ein Standard Limit von *MaxSize* = 128.

## <a name="returns"></a>Rückgabe

Gibt ein `dynamic` -Array (JSON) aller Werte von *Expr* in der Gruppe zurück.
Wenn die Eingabe für den `summarize` Operator nicht sortiert ist, ist die Reihenfolge der Elemente im resultierenden Array nicht definiert.
Wenn die Eingabe für den `summarize` Operator sortiert ist, verfolgt die Reihenfolge der Elemente im resultierenden Array die der Eingabe.

> [!TIP]
> Verwenden Sie die- [`array_sort_asc()`](./arraysortascfunction.md) oder- [`array_sort_desc()`](./arraysortdescfunction.md) Funktion, um eine geordnete Liste anhand eines Schlüssels zu erstellen.

## <a name="examples"></a>Beispiele

### <a name="one-column"></a>Eine Spalte

Das einfachste Beispiel besteht darin, eine Liste aus einer einzelnen Spalte zu erstellen:

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| summarize mylist = make_list(name)
```

|myList|
|---|
|["Dreieck", "Square", "Rechteck", "Pentagon", "hexseck", "heptagon", "Octogon", "Nonagon", "Decagon"]|

### <a name="using-the-by-clause"></a>Verwenden der by-Klausel

In der folgenden Abfrage gruppieren Sie mithilfe der- `by` Klausel:

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| summarize mylist = make_list(name) by isEvenSideCount = sideCount % 2 == 0
```

|myList|isevensidecount|
|---|---|
|false|["Dreieck", "Pentagon", "heptagon", "Nonagon"]|
|true|["Square", "Rechteck", "hexseck", "Octogon", "decseck"]|

### <a name="packing-a-dynamic-object"></a>Packen eines dynamischen Objekts

Sie können ein dynamisches Objekt in eine Spalte [packen](./packfunction.md) , bevor Sie eine Liste aus der Spalte erstellen, wie in der folgenden Abfrage gezeigt:

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| extend d = pack("name", name, "sideCount", sideCount)
| summarize mylist = make_list(d) by isEvenSideCount = sideCount % 2 == 0
```

|myList|isevensidecount|
|---|---|
|false|[{"Name": "Dreieck", "sidecount": 3}, {"Name": "Pentagon", "sidecount": 5}, {"Name": "heptagon", "sidecount": 7}, {"Name": "Nonagon", "sidecount": 9}]|
|true|[{"Name": "Square", "sidecount": 4}, {"Name": "Rechteck", "sidecount": 4}, {"Name": "hexseck", "sidecount": 6}, {"Name": "Octogon", "sidecount": 8}, {"Name": "decseck", "sidecount": 10}]|

## <a name="see-also"></a>Weitere Informationen

[`make_list_if`](./makelistif-aggfunction.md) der-Operator ähnelt `make_list` , mit dem Unterschied, dass er auch ein Prädikat akzeptiert.
