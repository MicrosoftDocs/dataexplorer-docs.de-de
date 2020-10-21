---
title: 'Such Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der Such Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 3305d78dd903160491ac3cdabd274ce8ace8ba2f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246424"
---
# <a name="lookup-operator"></a>lookup-Operator

Der- `lookup` Operator erweitert die Spalten einer Fakten Tabelle mit Werten, die in einer Dimensions Tabelle gesucht werden.

```kusto
FactTable | lookup kind=leftouter (DimensionTable) on CommonColumn, $left.Col1 == $right.Col2
```

Hier ist das Ergebnis eine Tabelle, die `FactTable` ( `$left` ) mit Daten aus erweitert `DimensionTable` (auf die von verwiesen wird `$right` ), indem eine Suche nach jedem Paar ( `CommonColumn` , `Col` ) aus der früheren Tabelle mit jedem Paar ( `CommonColumn1` , `Col2` ) in der letzteren Tabelle durchgeführt wird. Informationen zu den Unterschieden zwischen Fakten-und Dimensions Tabellen finden Sie unter [Fakten-und Dimensions Tabellen](../concepts/fact-and-dimension-tables.md). 

Der- `lookup` Operator führt einen Vorgang aus, der dem [Join-Operator](joinoperator.md) ähnelt und die folgenden Unterschiede hat:

* Das Ergebnis wiederholt keine Spalten aus der `$right` Tabelle, die als Grundlage für den Verknüpfungs Vorgang dienen.
* Es werden nur zwei Arten von Suche unterstützt, `leftouter` und `inner` , wobei `leftouter` die Standardeinstellung ist.
* Im Hinblick auf die Leistung geht das System standardmäßig davon aus, dass es sich bei der `$left` Tabelle um die größere Tabelle (Fakten Tabelle) handelt, und die `$right` Tabelle ist die kleinere Tabelle (Dimensions Tabelle). Dies entspricht genau der vom Operator verwendeten Annahme `join` .
* Der `lookup` Operator überträgt die `$right` Tabelle automatisch an die `$left` Tabelle (verhält sich im Wesentlichen so, als ob `hint.broadcast` angegeben wurde). Beachten Sie, dass dies die Größe der `$right` Tabelle einschränkt.

## <a name="syntax"></a>Syntax

*Linkfähig* `|` `lookup`[ `kind` `=` ( `leftouter` | `inner` )] `(` *Rightfähige* `)` `on` *Attribute*

## <a name="arguments"></a>Argumente

* *Lefables*: der Tabellen-oder Tabellen Ausdruck, der die Grundlage für die Suche ist.
  Wird als bezeichnet `$left` .

* *Righables*: der Tabellen-oder Tabellen Ausdruck, der zum Auffüllen neuer Spalten in der Fakten Tabelle verwendet wird. Wird als bezeichnet `$right` .

* *Attribute*: eine durch Trennzeichen getrennte Liste von einer oder mehreren Regeln, die beschreiben, wie Zeilen von *linksfähigen* Zeilen von *rightfähigen*abgeglichen werden. Mehrere Regeln werden mithilfe des `and` logischen Operators ausgewertet.
  Eine Regel kann eine der folgenden sein:

  |Regelart        |Syntax                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |Gleichheit nach Name |*ColumnName*                                    |`where`*Linksbar*. *ColumnName* `==` *Righbar*. *ColumnName*|
  |Gleichheit nach Wert|`$left.`*LeftColumn* `==` `$right.` *Rechte Spalte*|`where``$left.` *LeftColumn* `==` `$right.` * RightColumn        |

  > [!Note] 
  > Im Fall von ' Equality by value ' *müssen* die Spaltennamen mit der anwendbaren Besitzer Tabelle qualifiziert werden, die durch die `$left` -und- `$right` Notationen gekennzeichnet ist.

* `kind`: Eine optionale Anweisung zum Behandeln von Zeilen in " *lefables* ", die keine Entsprechung in " *rightions*" aufweisen. Standardmäßig `leftouter` wird verwendet. Dies bedeutet, dass alle Zeilen in der Ausgabe mit NULL-Werten angezeigt werden, die für die fehlenden Werte der von dem-Operator hinzugefügten *righ-* Spalten verwendet werden. Wenn `inner` verwendet wird, werden solche Zeilen in der Ausgabe weggelassen. (Andere Arten von Join werden vom p-Operator nicht unterstützt `looku` .)
  
## <a name="returns"></a>Rückgabe

Eine Tabelle mit:

* Einer Spalte für jede Spalte in jeder der beiden Tabellen, einschließlich der übereinstimmenden Schlüssel.
  Die Spalten der rechten Seite werden automatisch umbenannt, wenn Namenskonflikte vorliegen.
* Einer Zeile für jede Übereinstimmung zwischen den Eingabetabellen. Eine Übereinstimmung ist eine ausgewählte Zeile in einer Tabelle, die in allen `on` -Feldern denselben Wert wie eine Zeile in der anderen Tabelle aufweist. 
* Die Attribute (Suchschlüssel) werden nur einmal in der Ausgabe Tabelle angezeigt.

 * `kind` nicht angegeben `kind=leftouter`

     Zusätzlich zu den internen Übereinstimmungen ist eine Zeile für jede Zeile auf der linken (und/oder rechten) Seite vorhanden, selbst wenn es keine Übereinstimmung damit gibt. In diesem Fall enthalten die Ausgabezellen ohne Übereinstimmung NULL-Werte.

 * `kind=inner`

     Der Ausgabe enthält eine Zeile für jede Kombination von übereinstimmenden Zeilen der linken und rechten Seite.

## <a name="examples"></a>Beispiele

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
1       | Bill      | Schle    | billg
2       | Bill      | Clinton  | Billc
3       | Bill      | Clinton  | Billc
4       | Steve     | Ballmer  | SteveB
5       | Tim       | Koch     | TimC