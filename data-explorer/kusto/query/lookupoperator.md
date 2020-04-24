---
title: Suchoperator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Suchoperator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 1325a1b839b62baacade4cf7c64cd278aeeabaf5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513055"
---
# <a name="lookup-operator"></a>lookup-Operator

Der `lookup` Operator erweitert die Spalten einer Faktentabelle mit Werten, die in einer Dimensionstabelle nachgeschlagen sind.

```kusto
FactTable | lookup kind=leftouter (DimensionTable) on CommonColumn, $left.Col1 == $right.Col2
```

Hier bei diesem Ergebnis handelt es `FactTable` `$left`sich um `DimensionTable` eine Tabelle, `$right`die die ( ) mit`CommonColumn``Col`Daten aus (von )`CommonColumn1`erweitert, indem ein Nachschlagen jedes Paares ( , ) aus der früheren Tabelle mit jedem Paar ( ,`Col2`) in der letztgenannten Tabelle ausgeführt wird. Die Unterschiede zwischen Fakten- und Dimensionstabellen finden Sie unter [Fakten- und Dimensionstabellen](../concepts/fact-and-dimension-tables.md). 

Der `lookup` Operator führt einen Vorgang aus, der dem [Join-Operator](joinoperator.md) mit den folgenden Unterschieden ähnelt:

* Das Ergebnis wiederholt keine `$right` Spalten aus der Tabelle, die die Grundlage für den Verknüpfungsvorgang sind.
* Es werden nur zwei Arten `leftouter` `inner`von `leftouter` Nachschlagen unterstützt, und , wobei die Standardeinstellung ist.
* In Bezug auf die Leistung geht `$left` das System standardmäßig davon aus, `$right` dass es sich bei der Tabelle um die größere (Fakten-)Tabelle und die Tabelle um die kleinere (Dimensionen)-Tabelle handelt. Dies steht genau im Gegensatz `join` zu der Annahme des Operators.
* Der `lookup` Operator überträgt `$right` die `$left` Tabelle automatisch an die Tabelle `hint.broadcast` (im Wesentlichen verhält er sich so, als ob angegeben). Beachten Sie, dass dadurch `$right` die Größe der Tabelle begrenzt wird.

**Syntax**

*LeftTable* `|` `lookup` `kind` `=` [`leftouter`|`inner`( `(` )] `)` `on` *RightTable-Attribute* *RightTable*

**Argumente**

* *LeftTable*: Die Tabelle oder der Tabellenausdruck, der die Grundlage für die Suche ist.
  Bezeichnet als `$left`.

* *RightTable*: Die Tabelle oder der Tabellarische Ausdruck, der zum "Auffüllen" neuer Spalten in der Faktentabelle verwendet wird. Bezeichnet als `$right`.

* *Attribute*: Eine durch Kommas getrennte Liste mit einer oder mehreren Regeln, die beschreiben, wie Zeilen aus *LeftTable* mit Zeilen aus *RightTable*abgeglichen werden. Mehrere Regeln werden `and` mit dem logischen Operator ausgewertet.
  Eine Regel kann eine der wichtigsten sein:

  |Regelart        |Syntax                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |Gleichheit nach Namen |*ColumnName*                                    |`where`*LeftTable*. *ColumnName* `==` *RightTable*. *ColumnName*|
  |Gleichheit nach Wert|`$left.`*LeftColumn* `==` `$right.` *RightColumn*|`where``$left.` `$right.` *LeftColumn* `==` *RightColumn        |

  > [!Note] 
  > Bei "Gleichheit nach Wert" *müssen* die Spaltennamen mit der entsprechenden `$left` Besitzertabelle, die durch Notationen `$right` bezeichnet wird, qualifiziert werden.

* `kind`: Eine optionale Anweisung zum Behandeln von Zeilen in *LeftTable,* die in *RightTable*nicht übereinstimmen. Standardmäßig wird `leftouter` verwendet, was bedeutet, dass alle diese Zeilen in der Ausgabe mit NULL-Werten angezeigt werden, die für die fehlenden Werte von *RightTable-Spalten* verwendet werden, die vom Operator hinzugefügt wurden. Wenn `inner` diese Zeilen verwendet werden, werden sie in der Ausgabe weggelassen. (Andere Arten von Verknüpfungen `looku`werden vom p-Operator nicht unterstützt.)
  
**Rückgabe**

Eine Tabelle mit:

* Einer Spalte für jede Spalte in jeder der beiden Tabellen, einschließlich der übereinstimmenden Schlüssel.
  Die Spalten der rechten Seite werden automatisch umbenannt, wenn Namenskonflikte vorliegen.
* Einer Zeile für jede Übereinstimmung zwischen den Eingabetabellen. Eine Übereinstimmung ist eine ausgewählte Zeile in einer Tabelle, die in allen `on` -Feldern denselben Wert wie eine Zeile in der anderen Tabelle aufweist. 
* Die Attribute (Suchtasten) werden nur einmal in der Ausgabetabelle angezeigt.

 * `kind`Unspecified`kind=leftouter`

     Zusätzlich zu den internen Übereinstimmungen ist eine Zeile für jede Zeile auf der linken (und/oder rechten) Seite vorhanden, selbst wenn es keine Übereinstimmung damit gibt. In diesem Fall enthalten die Ausgabezellen ohne Übereinstimmung NULL-Werte.

 * `kind=inner`

     Der Ausgabe enthält eine Zeile für jede Kombination von übereinstimmenden Zeilen der linken und rechten Seite.

**Beispiele**

```kusto
let FactTable=datatable(Row:string,Personal:string,Family:string) [
  "1", "Bill",   "Gates",
  "2", "Bill",   "Clinton",
  "3", "Bill",   "Clinton",
  "4", "Steve",  "Ballmer",
  "5", "Tim",    "Cook"
];
let DimTable=datatable(Personal:string,Family:string,Alias:string) [
  "Bill",  "Gates",   "billg",
  "Bill",  "Clinton", "billc",
  "Steve", "Ballmer", "steveb",
  "Tim",   "Cook",    "timc"
];
FactTable
| lookup kind=leftouter DimTable on Personal, Family
```

Zeile     | Persönlich  | Familie   | Alias
--------|-----------|----------|--------
1       | Bill      | Gates    | billg
2       | Bill      | Clinton  | Billc
3       | Bill      | Clinton  | Billc
4       | Steve     | Ballmer  | Steveb
5       | Tim       | Kochen     | timc