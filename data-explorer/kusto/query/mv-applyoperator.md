---
title: mv-apply-Operator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den mv-apply-Operator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f24bf7721707aa1ba3ae9f0aad49b247f08c2498
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512307"
---
# <a name="mv-apply-operator"></a>mv-apply-Operator

Der Operator mv-apply erweitert jeden Datensatz in seiner Eingabetabelle in eine Untertabelle, wendet eine Unterabfrage auf jede Untertabelle an und gibt die Vereinigung der Ergebnisse aller Unterabfragen zurück.

Angenommen, eine Tabelle `T` hat `Metric` eine `dynamic` Spalte des Typs, deren Werte Arrays von `real` Zahlen sind. Die folgende Abfrage sucht die beiden `Metric` größten Werte in jedem Wert und gibt die Datensätze zurück, die diesen Werten entsprechen.

```kusto
T | mv-apply Metric to typeof(real) on (top 2 by Metric desc)
```

Im Allgemeinen kann der mv-apply-Operator als mit den folgenden Verarbeitungsschritten betrachtet werden:

1. Es verwendet den [operator mv-expand,](./mvexpandoperator.md) um jeden Datensatz in der Eingabe in Untertabellen zu erweitern.
2. Sie wendet die Unterabfrage für jede der Untertabellen an.
3. Es stellt jeder resultierenden Untertabelle null oder mehr Spalten vor, die die (bei Bedarf wiederholten) Werte der Quellspalten enthalten, die nicht erweitert werden.
4. Es gibt die Vereinigung der Ergebnisse zurück.

Der mv-expand-Operator erhält die folgenden Eingänge:

1. Ein oder mehrere Ausdrücke, die in dynamische Arrays ausgewertet werden, um sie zu erweitern.
   Die Anzahl der Datensätze in jeder erweiterten Untertabelle ist die maximale Länge jedes dieser dynamischen Arrays. (Wenn mehrere Ausdrücke angegeben werden, die entsprechenden Arrays jedoch unterschiedliche Längen aufweisen, werden bei Bedarf NULL-Werte eingeführt.)

2. Optional sind die Namen, die die Werte der Ausdrücke nach der Erweiterung zuweisen sollen.
   Diese werden zu den Namen der Spalten in den Untertabellen.
   Wenn nicht angegeben, wird der ursprüngliche Name der Spalte verwendet (wenn der Ausdruck ein Spaltenverweis ist), oder ein zufälliger Name wird verwendet (andernfalls).

   > [!NOTE]
   > Es wird empfohlen, die Standardspaltennamen zu verwenden.

3. Die Datentypen der Elemente dieser dynamischen Arrays nach der Erweiterung.
   Diese werden zu den Spaltentypen der Spalten in den Untertabellen.
   Wenn dieser nicht angegeben wurde, wird `dynamic` verwendet.

4. Optional ist der Name einer Spalte, die den Untertabellen hinzugefügt werden soll, die den 0-basierten Index des Elements im Array angibt, das zum Untertabellendatensatz geführt hat.

5. Optional wird die maximale Anzahl der zu erweiternden Arrayelemente angezeigt.

Der mv-apply-Operator kann als Verallgemeinerung des [mv-expand-Operators](./mvexpandoperator.md) betrachtet werden (der letztere kann von ersteren implementiert werden, wenn die Unterabfrage nur Projektionen enthält).)

**Syntax**

*T* `|` T `mv-apply` [*ItemIndex*] *ColumnsToExpand* [*RowLimit*] `on` `(` *SubQuery*`)`

Wobei *ItemIndex* die Syntax hat:

`with_itemindex``=` *IndexColumnName*

*ColumnsToExpand* ist eine durch Kommas getrennte Liste eines oder mehrerer Elemente des Formulars:

[*Name* `=`] *ArrayExpression* `to` `typeof` `(`[ *Typname*`)`]

*RowLimit* ist einfach:

`limit`*RowLimit*

und *SubQuery* hat die gleiche Syntax wie jede Abfrageanweisung.

**Argumente**

* *ItemIndex*: Wenn verwendet, gibt den `long` Namen einer Spalte des Typs an, die als Teil der Array-Erweiterungsphase an die Eingabe angehängt wird, und gibt den 0-basierten Arrayindex des erweiterten Werts an.

* *Name*: Wenn verwendet, der Name, um die Array-erweiterten Werte jedes Array-erweiterten Ausdrucks zuzuweisen.
  (Wenn nicht angegeben, wird der Name der Spalte verwendet, wenn verfügbar, oder ein zufälliger Name wird generiert, wenn *ArrayExpression* kein einfacher Spaltenname ist.)

* *ArrayExpression*: Ein `dynamic` Ausdruck des Typs, dessen Werte von Arrays erweitert werden.
  Wenn der Ausdruck der Name einer Spalte in der Eingabe ist, wird die Eingabespalte aus der Eingabe entfernt, und eine neue Spalte mit demselben Namen (oder *ColumnName,* falls angegeben), wird in der Ausgabe angezeigt.

* *Typname*: Wenn verwendet, der Name des Typs, den die einzelnen Elemente des `dynamic` *ArrayExpression-ArrayExpression* verwenden. Elemente, die diesem Typ nicht entsprechen, werden durch einen NULL-Wert ersetzt.
  (Wenn nicht `dynamic` angegeben, wird standardmäßig verwendet.)

* *RowLimit*: Wenn verwendet, eine Begrenzung für die Anzahl der Datensätze, die aus jedem Datensatz der Eingabe generiert werden sollen.
  (Wenn nicht angegeben, wird 2147483647 verwendet.)

* *SubQuery*: Ein tabellarischer Abfrageausdruck mit einer impliziten Tabellenquelle, der auf jede Array-erweiterte Untertabelle angewendet wird.

**Hinweise**

* Im Gegensatz zum [mv-expand-Operator](./mvexpandoperator.md) unterstützt der mv-apply-Operator nur die Arrayerweiterung. Es gibt keine Unterstützung für den Ausbau von Immobilientaschen.

**Beispiele**

## <a name="getting-the-largest-element-from-the-array"></a>Abrufen des größten Elements aus dem Array

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

|xMod2|l           |element|
|-----|------------|-------|
|1    |[1, 3, 5, 7]|7      |
|0    |[2, 4, 6, 8]|8      |

## <a name="calculating-sum-of-largest-two-elments-in-an-array"></a>Berechnung der Summe der größten zwei Elments in einem Array

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

|xMod2|l        |SumOfTop2|
|-----|---------|---------|
|1    |[1,3,5,7]|12       |
|0    |[2,4,6,8]|14       |


## <a name="using-with_itemindex-for-working-with-subset-of-the-array"></a>Verwenden `with_itemindex` für die Arbeit mit Teilmenge des Arrays

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

## <a name="using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key"></a>Verwenden `mv-apply` des Operators zum `makelist` Sortieren der Ausgabe von Aggregat nach Schlüsseln

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

|user_id|list_command_details_command|
|---|---|
|user1|[<br>  "ls",<br>  "mkdir",<br>  "chmod",<br>  "dir",<br>  "pwd",<br>  "rm"<br>]|
|user2|[<br>  "rm",<br>  "pwd"<br>]|


**Siehe auch**

* [mv-expand](./mvexpandoperator.md) Operator.