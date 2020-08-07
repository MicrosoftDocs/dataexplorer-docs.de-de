---
title: 'MV-APPLY-Operator: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt den MV-APPLY-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 656d1d5d9120baebced624fee476d261214eff57
ms.sourcegitcommit: 83202ec6fec0ce98fdf993bbb72adc985d6d9c78
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87871849"
---
# <a name="mv-apply-operator"></a>mv-apply-Operator

Wendet eine Unterabfrage auf jeden Datensatz an und gibt die Vereinigung der Ergebnisse aller Unterabfragen zurück.

Nehmen Sie beispielsweise an, eine Tabelle `T` enthält eine Spalte `Metric` vom Typ, `dynamic` deren Werte `real` Zahlen Arrays sind. Mit der folgenden Abfrage werden die beiden größten Werte in jedem `Metric` Wert gesucht und die Datensätze zurückgegeben, die diesen Werten entsprechen.

```kusto
T | mv-apply Metric to typeof(real) on 
(
   top 2 by Metric desc
)
```

Der `mv-apply` Operator verfügt über die folgenden Verarbeitungsschritte:

1. Verwendet den- [`mv-expand`](./mvexpandoperator.md) Operator, um jeden Datensatz in der Eingabe in untergeordnete Tabellen zu erweitern.
1. Wendet die Unterabfrage für jede der untergeordneten Tabellen an.
1. Fügt der resultierenden unter Tabelle 0 (null) oder mehr Spalten hinzu. Diese Spalten enthalten die Werte der Quell Spalten, die nicht erweitert werden und bei Bedarf wiederholt werden.
1. Gibt die Gesamtmenge der Ergebnisse zurück.

Der `mv-apply` Operator erhält die folgenden Eingaben:

1. Mindestens ein Ausdruck, der in dynamische Arrays ausgewertet wird, die erweitert werden sollen.
   Die Anzahl der Datensätze in jeder erweiterten unter Tabelle ist die maximale Länge der einzelnen dynamischen Arrays. NULL-Werte werden hinzugefügt, wenn mehrere Ausdrücke angegeben werden und die entsprechenden Arrays unterschiedlich lang sind.

1. Optional die Namen, die nach der Erweiterung den Werten der Ausdrücke zugewiesen werden sollen.
   Diese Namen werden zu den Spaltennamen in den untergeordneten Tabellen.
   Wenn nicht angegeben, wird der ursprüngliche Name der Spalte verwendet, wenn der Ausdruck ein Spalten Verweis ist. Andernfalls wird ein zufälliger Name verwendet. 

   > [!NOTE]
   > Es wird empfohlen, die Standard Spaltennamen zu verwenden.

1. Die Datentypen der Elemente dieser dynamischen Arrays nach der Erweiterung.
   Diese werden zu den Spaltentypen der Spalten in den untergeordneten Tabellen.
   Wenn dieser nicht angegeben wurde, wird `dynamic` verwendet.

1. Optional der Name einer Spalte, die den untergeordneten Tabellen hinzugefügt werden soll, die den 0-basierten Index des Elements im Array angibt, das zum Teil Tabellendaten Satz führte.

1. Optional die maximale Anzahl von Array Elementen, die erweitert werden sollen.

Der `mv-apply` Operator kann sich als Generalisierung des [`mv-expand`](./mvexpandoperator.md) Operators vorstellen (der zweite kann tatsächlich von der ersten implementiert werden, wenn die Unterabfrage nur Projektionen einschließt).

## <a name="syntax"></a>Syntax

*T* `|` `mv-apply` [*itemIndex*] *columnstoexpand* [*ROWLIMIT*] `on` `(` *Unterabfrage*`)`

Dabei hat *itemIndex* die Syntax:

`with_itemindex``=` *Indexcolumnname*

*Columnstoexpand* ist eine durch Trennzeichen getrennte Liste mit einem oder mehreren Elementen in der Form:

[*Name* `=` ] *Arrayexpression* [ `to` `typeof` `(` *Typname* `)` ]

*ROWLIMIT* ist einfach:

`limit`*ROWLIMIT*

und die *Unterabfrage* haben dieselbe Syntax wie jede beliebige Abfrage Anweisung.

## <a name="arguments"></a>Argumente

* *ItemIndex*: gibt bei Verwendung den Namen einer Spalte vom Typ an, die `long` als Teil der Array Erweiterungsphase an die Eingabe angehängt wird, und gibt den 0-basierten Array Index des erweiterten Werts an.

* *Name*: bei Verwendung wird der Name zum Zuweisen der durch das Array erweiterten Werte jedes Arrays erweiterter Ausdrucks verwendet.
  Wenn nicht angegeben, wird der Name der Spalte verwendet, falls verfügbar.
  Ein zufälliger Name wird generiert, wenn *arrayexpression* kein einfacher Spaltenname ist.

* *Arrayexpression*: ein Ausdruck vom Typ, `dynamic` dessen Werte durch ein Array erweitert werden.
  Wenn der Ausdruck der Name einer Spalte in der Eingabe ist, wird die Eingabe Spalte aus der Eingabe entfernt, und in der Ausgabe wird eine neue Spalte mit demselben Namen (oder *ColumnName* ) angezeigt.

* *Typname*: bei Verwendung wird der Name des Typs, der von den einzelnen Elementen des `dynamic` *arrayexpression* -Arrays übernommen wird. Elemente, die diesem Typ nicht entsprechen, werden durch einen NULL-Wert ersetzt.
  (Falls nicht angegeben, `dynamic` wird standardmäßig verwendet.)

* *ROWLIMIT*: bei Verwendung ein Grenzwert für die Anzahl der Datensätze, die von jedem Datensatz der Eingabe generiert werden sollen.
  (Wenn kein Wert angegeben wird, wird 2147483647 verwendet.)

* *Unterabfrage*: ein tabellarischer Abfrage Ausdruck mit einer impliziten tabellarischen Quelle, die auf jede Array-erweiterte unter Tabelle angewendet wird.

**Hinweise**

* Im Gegensatz zum- [`mv-expand`](./mvexpandoperator.md) Operator `mv-apply` unterstützt der-Operator nur die Array Erweiterung. Es gibt keine Unterstützung für das Erweitern von Eigenschaften Beuteln.

## <a name="examples"></a>Beispiele

## <a name="getting-the-largest-element-from-the-array"></a>Das größte Element wird aus dem Array erhalten.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data =
range x from 1 to 8 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply element=l to typeof(long) on 
(
   top 1 by element
)
```

|`xMod2`|l           |element|
|-----|------------|-------|
|1    |[1, 3, 5, 7]|7      |
|0    |[2, 4, 6, 8]|8      |

## <a name="calculating-the-sum-of-the-largest-two-elements-in-an-array"></a>Berechnen der Summe der größten zwei Elemente in einem Array

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data =
range x from 1 to 8 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply l to typeof(long) on
(
   top 2 by l
   | summarize SumOfTop2=sum(l)
)
```

|`xMod2`|l        |SumOfTop2|
|-----|---------|---------|
|1    |[1, 3, 5, 7]|12       |
|0    |[2, 4, 6, 8]|14       |


## <a name="using-with_itemindex-for-working-with-a-subset-of-the-array"></a>Verwenden `with_itemindex` von zum Arbeiten mit einer Teilmenge des Arrays

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data =
range x from 1 to 10 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply with_itemindex=index element=l to typeof(long) on 
(
   // here you have 'index' column
   where index >= 3
)
| project index, element
```

|Index|element|
|---|---|
|3|7|
|4|9|
|3|8|
|4|10|

## <a name="using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key"></a>Verwenden des `mv-apply` Operators zum Sortieren der Ausgabe des `makelist` Aggregats nach einem Schlüssel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(command:string, command_time:datetime, user_id:string)
[
    'chmod',        datetime(2019-07-15),   "user1",
    'ls',           datetime(2019-07-02),   "user1",
    'dir',          datetime(2019-07-22),   "user1",
    'mkdir',        datetime(2019-07-14),   "user1",
    'rm',           datetime(2019-07-27),   "user1",
    'pwd',          datetime(2019-07-25),   "user1",
    'rm',           datetime(2019-07-23),   "user2",
    'pwd',          datetime(2019-07-25),   "user2",
]
| summarize commands_details = make_list(pack('command', command, 'command_time', command_time)) by user_id
| mv-apply command_details = commands_details on
(
    order by todatetime(command_details['command_time']) asc
    | summarize make_list(tostring(command_details['command']))
)
| project-away commands_details
```

|`user_id`|`list_command_details_command`|
|---|---|
|user1|[<br>  "ls",<br>  "mkdir",<br>  "chmod",<br>  "dir",<br>  "pwd",<br>  RMS<br>]|
|user2|[<br>  "RM",<br>  PWD<br>]|


**Weitere Informationen**

* [MV-Expand-](./mvexpandoperator.md) Operator.
