---
title: make_bag() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird make_bag() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 16f5f5663c4807a766d99c12020ff0a46c4db336
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512987"
---
# <a name="make_bag-aggregation-function"></a>make_bag() (Aggregationsfunktion)

Gibt `dynamic` einen (JSON) Eigenschaftenbeutel (Wörterbuch) aller Werte von *Expr* in der Gruppe zurück.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`summarize``make_bag(` *Expr* `,` [ *MaxSize*]`)`

**Argumente**

* *Expr*: Ausdruck `dynamic` des Typs, der für die Aggregationsberechnung verwendet wird.
* *MaxSize* ist ein optionaler Ganzzahlgrenzwert für die maximale Anzahl der zurückgegebenen Elemente (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

**Hinweis**

Eine veraltete und veraltete `make_dictionary()` Variante dieser Funktion: hat ein Standardlimit von *MaxSize* = 128.

**Rückgabe**

Gibt `dynamic` einen (JSON) Eigenschaftenbeutel (Wörterbuch) aller Werte von *Expr* in der Gruppe zurück, die Eigenschafts-Taschen (Wörterbücher) sind.
Nicht-Wörterbuchwerte werden übersprungen.
Wenn ein Schlüssel in mehr als einer Zeile angezeigt wird, wird ein beliebiger Wert (aus den möglichen Werten für diesen Schlüssel) ausgewählt.

**Siehe auch**

Verwenden Sie das [bag_unpack()-Plugin,](bag-unpackplugin.md) um dynamische JSON-Objekte mithilfe von Eigenschaftenbeutelschlüsseln in Spalten zu erweitern. 

**Beispiele**

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
|" prop01": "val_a", "prop02": "val_b", "prop03": "val_c" |

Verwenden Sie [bag_unpack()](bag-unpackplugin.md) Plugin, um die Taschenschlüssel im make_bag() Ausgang in Spalten umzuwandeln. 

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