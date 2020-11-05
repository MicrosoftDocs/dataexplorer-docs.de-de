---
title: 'User-Defined Funktionen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden User-Defined Funktionen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e611bfb1d5dbf0122553b223a200400c526c975f
ms.sourcegitcommit: 42cc7d11f41a5bfa9e021764b044dcd68d99a258
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93403747"
---
# <a name="user-defined-functions"></a>Benutzerdefinierte Funktionen

**Benutzerdefinierte Funktionen** sind wiederverwendbare Unterabfragen, die als Teil der Abfrage selbst definiert werden können ( **Ad-hoc-Funktionen** ) oder als Teil der Daten Bank Metadaten ( **gespeicherte Funktionen** ) persistent gespeichert werden. Benutzerdefinierte Funktionen werden durch einen **Namen** aufgerufen, werden mit 0 (null) oder mehr **Eingabe Argumenten** bereitgestellt (bei denen es sich um skalare oder tabellarische Werte handeln kann), und Sie können basierend auf **dem Funktions Rumpf** einen einzelnen Wert (der Skalar oder tabellarisch sein) liefern.

Eine benutzerdefinierte Funktion gehört zu einer von zwei Kategorien:

* Skalarfunktionen 
* Tabellarische Funktionen 

Die Eingabeargumente und die Ausgabe der Funktion bestimmen, ob es sich um einen skalaren oder tabellarischen Wert handelt, der dann festlegt, wie Sie verwendet werden kann. 

## <a name="scalar-function"></a>Skalarfunktion

* Hat keine Eingabeargumente, oder alle Eingabeargumente sind skalare Werte.
* Erzeugt einen einzelnen Skalarwert.
* Kann überall dort verwendet werden, wo ein skalarer Ausdruck zulässig ist.
* Darf nur den Zeilen Kontext verwenden, in dem er definiert ist.
* Kann nur auf Tabellen (und Sichten) verweisen, die sich im Schema befinden, auf das zugegriffen werden kann.

## <a name="tabular-function"></a>Tabellarische Funktion

* Akzeptiert mindestens ein tabellarisches Eingabe Argument und NULL oder mehr skalare Eingabeargumente und/oder:
* Erzeugt einen einzelnen tabellarischen Wert

## <a name="function-names"></a>Funktionsnamen

Gültige Namen von benutzerdefinierten Funktionen müssen denselben [bezeichnerbenennungs Regeln](../schema-entities/entity-names.md#identifier-naming-rules) wie andere Entitäten entsprechen.

Der Name muss auch innerhalb des Definitions Bereichs eindeutig sein.

> [!NOTE]
> Wenn eine gespeicherte Funktion und eine Tabelle denselben Namen aufweisen, überschreibt die gespeicherte Funktion beim Abfragen des Tabellen-/Funktionsnamens einen überschreiben.

## <a name="input-arguments"></a>Eingabeargumente

Gültige benutzerdefinierte Funktionen befolgen die folgenden Regeln:

* Eine benutzerdefinierte Funktion verfügt über eine stark typisierte Liste von NULL oder mehr Eingabe Argumenten.
* Ein Eingabe Argument hat einen Namen, einen Typ und (für skalare Argumente) einen [Standardwert](#default-values).
* Der Name eines Eingabe Arguments ist ein Bezeichner.
* Der Typ eines Eingabe Arguments ist entweder einer der skalaren Datentypen oder ein tabellarisches Schema.

Syntaktisch ist die Liste der Eingabeargumente eine durch Trennzeichen getrennte Liste von Argument Definitionen, die in Klammern umschließt werden. Jede Argument Definition ist als angegeben.

```
ArgName:ArgType [= ArgDefaultValue]
```
 Bei tabellarischen Argumenten hat *argtype* die gleiche Syntax wie die Tabellendefinition (Klammer und eine Liste von Spaltenname-/typpaaren), mit der zusätzlichen Unterstützung für `(*)` "beliebiges tabellarisches Schema".

Beispiel:

|Syntax                        |Beschreibung der Eingabe Argumentliste                                 |
|------------------------------|-----------------------------------------------------------------|
|`()`                          |Keine Argumente|
|`(s:string)`                  |Einzelnes skalare Argument, das `s` einen Wert vom Typ "" annimmt `string`|
|`(a:long, b:bool=true)`       |Zwei skalare Argumente, wobei der zweite Wert über einen Standardwert verfügt.    |
|`(T1:(*), T2(r:real), b:bool)`|Drei Argumente (zwei tabellarische Argumente und ein Skalar-Argument)  |

> [!NOTE]
> Wenn Sie sowohl tabellarische Eingabeargumente als auch skalare Eingabeargumente verwenden, platzieren Sie alle tabellarischen Eingabeargumente vor den skalaren Eingabe Argumenten.

## <a name="examples"></a>Beispiele

Eine skalare Funktion:

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

Eine Tabellen Funktion, die keine Argumente annimmt:

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

Eine tabellarische Funktion, die sowohl eine tabellarische Eingabe als auch eine skalare Eingabe annimmt:

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v
};
MyFilter((range x from 1 to 10 step 1), 9)
```

|x|
|---|
|9|
|10|

Eine tabellarische Funktion, die eine tabellarische Eingabe ohne angegebene Spalte verwendet.
Jede Tabelle kann an eine Funktion übermittelt werden, und in der Funktion kann auf keine Tabellen Spalten verwiesen werden.

```kusto
let MyDistinct = (T:(*)) {
  T | distinct *
};
MyDistinct((range x from 1 to 3 step 1))
```

|x|
|---|
|1|
|2|
|3|

## <a name="declaring-user-defined-functions"></a>Deklarieren von benutzerdefinierten Funktionen

Die Deklaration einer benutzerdefinierten Funktion stellt Folgendes bereit:

* Funktions **Name**
* Funktions **Schema** (Parameter, die es akzeptiert, sofern vorhanden)
* Funktions **Rumpf**

> [!Note]
> Überladende Funktionen werden nicht unterstützt. Sie können nicht mehrere Funktionen mit dem gleichen Namen und unterschiedlichen Eingabe Schemas erstellen.

> [!TIP]
> Lambda-Funktionen haben keinen Namen und werden mithilfe einer [Let-Anweisung](../letstatement.md)an einen Namen gebunden. Daher können Sie als benutzerdefinierte gespeicherte Funktionen angesehen werden.
> Beispiel: Deklaration für eine Lambda-Funktion, die zwei Argumente akzeptiert (eine `string` aufgerufene `s` und eine `long` aufgerufene `i` ). Sie gibt das Produkt des ersten zurück (nachdem es in eine Zahl umgewandelt wurde) und das zweite. Der Lambda-Ausdruck ist an den Namen gebunden `f` :

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

Der Funktions **Rumpf** umfasst Folgendes:

* Genau ein Ausdruck, der den Rückgabewert der Funktion (Skalar-oder tabellarischer Wert) bereitstellt.
* Eine beliebige Zahl (0 (null) oder mehr) von [Let-Anweisungen](../letstatement.md), deren Gültigkeitsbereich der des Funktions Texts ist. Wenn angegeben, müssen die Let-Anweisungen dem Ausdruck vorangestellt sein, der den Rückgabewert der Funktion definiert.
* Eine beliebige Zahl (0 (null) oder mehr) der [Abfrage Parameter Anweisungen](../queryparametersstatement.md), die die von der Funktion verwendeten Abfrage Parameter deklarieren. Wenn angegeben, müssen Sie dem Ausdruck vorangestellt sein, der den Rückgabewert der Funktion definiert.

> [!NOTE]
> Andere Arten von [Abfrage Anweisungen](../statements.md) , die auf der obersten Ebene der Abfrage unterstützt werden, werden innerhalb eines Funktions Texts nicht unterstützt.

### <a name="examples-of-user-defined-functions"></a>Beispiele für benutzerdefinierte Funktionen 

**Benutzerdefinierte Funktion, die eine Let-Anweisung verwendet**

Im folgenden Beispiel wird der Name `Test` an eine benutzerdefinierte Funktion (Lambda) gebunden, die drei Let-Anweisungen verwendet. Die Ausgabe lautet `70` :

```kusto
let Test1 = (id: int) {
  let Test2 = 10;
  let Test3 = 10 + Test2 + id;
  let Test4 = (arg: int) {
      let Test5 = 20;
      Test2 + Test3 + Test5 + arg
  };
  Test4(10)
};
range x from 1 to Test1(10) step 1
| count
```

**Eine benutzerdefinierte Funktion, die einen Standardwert für einen Parameter definiert.**

Das folgende Beispiel zeigt eine Funktion, die drei Argumente akzeptiert. Die beiden letzteren haben einen Standardwert und müssen nicht an der Website des Aufrufes vorhanden sein.

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>Aufrufen einer benutzerdefinierten Funktion

Eine benutzerdefinierte Funktion, die keine Argumente annimmt, kann entweder durch ihren Namen oder durch ihren Namen und eine leere Argumentliste in Klammern aufgerufen werden.

Beispiele:

```kusto
// Bind the identifier a to a user-defined function (lambda) that takes
// no arguments and returns a constant of type long:
let a=(){123};
// Invoke the function in two equivalent ways:
range x from 1 to 10 step 1
| extend y = x * a, z = x * a() 
```

```kusto
// Bind the identifier T to a user-defined function (lambda) that takes
// no arguments and returns a random two-by-two table:
let T=(){
  range x from 1 to 2 step 1
  | project x1 = rand(), x2 = rand()
};
// Invoke the function in two equivalent ways:
// (Note that the second invocation must be itself wrapped in
// an additional set of parentheses, as the union operator
// differentiates between "plain" names and expressions)
union T, (T())
```

Eine benutzerdefinierte Funktion, die ein oder mehrere skalare Argumente annimmt, kann mit dem Tabellennamen und einer konkreten Argumentliste in Klammern aufgerufen werden:

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

Eine benutzerdefinierte Funktion, die ein oder mehrere Tabellen Argumente (und beliebig viele skalare Argumente) annimmt, kann mit dem Tabellennamen und einer konkreten Argumentliste in Klammern aufgerufen werden:

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

Sie können auch den-Operator verwenden `invoke` , um eine benutzerdefinierte Funktion aufzurufen, die ein oder mehrere Tabellen Argumente annimmt und eine Tabelle zurückgibt. Diese Funktion ist nützlich, wenn das erste konkrete Tabellen Argument der Funktion die Quelle des Operators ist `invoke` :

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>Standardwerte

Funktionen können unter den folgenden Bedingungen Standardwerte für einige ihrer Parameter bereitstellen:

* Standardwerte können nur für skalare Parameter bereitgestellt werden.
* Standardwerte sind immer Literale (Konstanten). Sie können keine willkürlichen Berechnungen sein.
* Parameter ohne Standardwert stehen immer vor Parametern, die über einen Standardwert verfügen.
* Aufrufer müssen den Wert aller Parameter ohne Standardwerte in derselben Reihenfolge wie die Funktionsdeklaration bereitstellen.
* Aufrufer müssen nicht den Wert für Parameter mit Standardwerten bereitstellen, dies kann jedoch der Fall sein.
* Aufrufer können Argumente in einer Reihenfolge bereitstellen, die nicht der Reihenfolge der Parameter entspricht. Wenn dies der Fall ist, müssen Sie Ihre Argumente benennen.

Im folgenden Beispiel wird eine Tabelle mit zwei identischen Datensätzen zurückgegeben. Beim ersten Aufruf von `f` werden die Argumente vollständig "verschrottet", sodass jedem explizit ein Name zugewiesen wird:

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
union
  (print x=f(c=7, a=12)), // "12-b.default-7"
  (print x=f(12, c=7))    // "12-b.default-7"
```

|x|
|---|
|12-b. Standard-7|
|12-b. Standard-7|

## <a name="view-functions"></a>Funktionen anzeigen

Eine benutzerdefinierte Funktion, die keine Argumente annimmt und einen tabellarischen Ausdruck zurückgibt, kann als **Sicht** gekennzeichnet werden. Das Markieren einer benutzerdefinierten Funktion als Sicht bedeutet, dass sich die Funktion wie eine Tabelle verhält, wenn die Namensauflösung für die Platzhalter Tabelle durchgeführt wird.
Das folgende Beispiel zeigt zwei benutzerdefinierte Funktionen, `T_view` und `T_notview` , und zeigt, wie nur der erste durch den Platzhalter Verweis in aufgelöst wird `union` :

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="restrictions"></a>Beschränkungen

Es gelten folgende Einschränkungen:

* Benutzerdefinierte Funktionen können nicht in aufzurufende [()](../toscalarfunction.md) Aufruf Informationen übergeben werden, die von dem Zeilen Kontext abhängen, in dem die Funktion aufgerufen wird.
* Benutzerdefinierte Funktionen, die einen tabellarischen Ausdruck zurückgeben, können nicht mit einem Argument aufgerufen werden, das sich vom Zeilen Kontext unterscheidet.
* Eine Funktion, die mindestens eine tabellarische Eingabe annimmt, kann nicht auf einem Remote Cluster aufgerufen werden.
* Eine skalare Funktion kann nicht auf einem Remote Cluster aufgerufen werden.

Die einzige Stelle, an der eine benutzerdefinierte Funktion mit einem Argument aufgerufen werden kann, das mit dem Zeilen Kontext variiert, ist, wenn die benutzerdefinierte Funktion nur aus skalaren Funktionen besteht und nicht verwendet `toscalar()` .

**Beispiel für Einschränkung 1**

```kusto
// Supported:
// f is a scalar function that doesn't reference any tabular expression
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { now() + hours*1h };
Table2 | where Column != 123 | project d = f(10)

// Supported:
// f is a scalar function that references the tabular expression Table1,
// but is invoked with no reference to the current row context f(10):
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { toscalar(Table1 | summarize min(xdate) - hours*1h) };
Table2 | where Column != 123 | project d = f(10)

// Not supported:
// f is a scalar function that references the tabular expression Table1,
// and is invoked with a reference to the current row context f(Column):
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { toscalar(Table1 | summarize min(xdate) - hours*1h) };
Table2 | where Column != 123 | project d = f(Column)
```

**Beispiel für Einschränkung 2**

```kusto
// Not supported:
// f is a tabular function that is invoked in a context
// that expects a scalar value.
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { range x from 1 to hours step 1 | summarize make_list(x) };
Table2 | where Column != 123 | project d = f(Column)
```

## <a name="features-that-are-currently-unsupported-by-user-defined-functions"></a>Funktionen, die derzeit nicht von benutzerdefinierten Funktionen unterstützt werden

Aus Gründen der Vollständigkeit finden Sie hier einige häufig angeforderte Features für benutzerdefinierte Funktionen, die derzeit nicht unterstützt werden:

1.  Funktions Überladung: derzeit gibt es keine Möglichkeit, eine Funktion zu überladen (d. h., mehrere Funktionen mit dem gleichen Namen und einem anderen Eingabe Schema zu erstellen).

2.  Standardwerte: der Standardwert für einen skalarparameter für eine Funktion muss ein skalares Literale (konstant) sein. Außerdem können gespeicherte Funktionen keinen Standardwert vom Typ aufweisen `dynamic` .
