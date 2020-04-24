---
title: make_bag_if() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird make_bag_if() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 201de637df387bb8925995a5e52c048255d1535c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512970"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if() (Aggregationsfunktion)

Gibt `dynamic` einen (JSON) Eigenschaftenbeutel (Wörterbuch) aller Werte von *Expr* in der Gruppe `true`zurück, für die *Predikat* ausgewertet wird.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`summarize``make_bag_if(` *Expr*, *Prädikat* [`,` *MaxSize*]`)`

**Argumente**

* *Expr*: Ausdruck `dynamic` des Typs, der für die Aggregationsberechnung verwendet wird.
* *Prädikat*: Prädikat, das `true`auf ausgewertet werden muss, damit *Expr* dem Ergebnis hinzugefügt werden kann.
* *MaxSize* ist ein optionaler Ganzzahlgrenzwert für die maximale Anzahl der zurückgegebenen Elemente (Standardwert ist *1048576*). Der MaxSize-Wert darf 1048576 nicht überschreiten.

**Rückgabe**

Gibt `dynamic` einen (JSON) Eigenschaften-Beutel (Wörterbuch) aller Werte von *Expr* in der Gruppe zurück, die Eigenschafts-Taschen (Wörterbücher) sind, für die *Predikat* ausgewertet wird. `true`
Nicht-Wörterbuchwerte werden übersprungen.
Wenn ein Schlüssel in mehr als einer Zeile angezeigt wird, wird ein beliebiger Wert (aus den möglichen Werten für diesen Schlüssel) ausgewählt.

**Siehe auch**

[`make_bag`](./make-bag-aggfunction.md)Funktion, die dasselbe tut, ohne Prädikatsausdruck.

**Beispiele**

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
|" prop01": "val_a", "prop03": "val_c" |

Verwenden Sie [bag_unpack()-Plugin,](bag-unpackplugin.md) um die Taschenschlüssel im make_bag_if()-Ausgang in Spalten umzuwandeln. 

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