---
title: Benutzerdefinierte Funktionen – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden benutzerdefinierte Funktionen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.localizationpriority: high
ms.openlocfilehash: 92627b3325a7a2ba8e2e4d58a82ebf6db3977221
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512875"
---
# <a name="user-defined-functions"></a>Benutzerdefinierte Funktionen

**Benutzerdefinierte Funktionen** sind wiederverwendbare Unterabfragen, die als Teil der jeweiligen Abfrage selbst (**Ad-hoc-Funktionen**) definiert werden oder als Bestandteil der Datenbankmetadaten (**gespeicherte Funktionen**) gespeichert sein können. Benutzerdefinierte Funktionen werden über einen **Namen** aufgerufen, können mit **Eingabeargumenten** (die skalar oder tabellarisch sein können) bereitgestellt werden, und erstellen einen einzelnen Wert (der skalar oder tabellarisch sein kann) basierend auf dem **Rumpf** der Funktion.

Eine benutzerdefinierte Funktion gehört zu einer der beiden folgenden Kategorien:

* Skalarfunktionen 
* Tabellarische Funktionen 

Die Eingabeargumente und die Ausgabe einer Funktion bestimmen, ob sie eine Skalar- oder tabellarische Funktion ist, wodurch dann festlegt wird, wie sie verwendet werden kann. 

## <a name="scalar-function"></a>Skalarfunktion

* Hat keine Eingabeargumente, oder alle ihrer Eingabeargumente sind skalare Werte
* Erzeugt einen einzelnen Skalarwert
* Kann überall dort verwendet werden, wo ein skalarer Ausdruck zulässig ist
* Darf nur den Zeilenkontext verwenden, in dem sie definiert ist
* Kann nur auf Tabellen (und Sichten) verweisen, die sich in dem Schema befinden, auf das zugegriffen werden kann

## <a name="tabular-function"></a>Tabellarische Funktion

* Akzeptiert mindestens ein tabellarisches Eingabeargument und optional skalare Eingabeargumente, und/oder:
* Erzeugt einen einzelnen tabellarischen Wert

## <a name="function-names"></a>Funktionsnamen

Für einen gültigen Namen einer benutzerdefinierten Funktion gelten dieselben [Benennungsregeln für Bezeichner](../schema-entities/entity-names.md#identifier-naming-rules) wie für andere-Entitäten.

Der jeweilige Name muss außerdem innerhalb seines Definitionsbereichs eindeutig sein.

> [!NOTE]
> Haben eine gespeicherte Funktion und eine Tabelle denselben Namen, hat die gespeicherte Funktion Vorrang, wenn der Tabellen-/Funktionsname abgefragt wird.

## <a name="input-arguments"></a>Eingabeargumente

Für eine gültige benutzerdefinierte Funktion gelten die folgenden Regeln:

* Eine benutzerdefinierte Funktion kann eine stark typisierte Liste mit Eingabeargumenten haben.
* Ein Eingabeargument hat einen Namen, einen Typ und (für skalare Argumente) einen [Standardwert](#default-values).
* Der Name eines Eingabearguments ist ein Bezeichner.
* Der Typ eines Eingabearguments ist entweder einer der skalaren Datentypen oder ein tabellarisches Schema.

Syntaktisch ist die Eingabeargumentenliste eine durch Trennzeichen getrennte Liste von Argumentdefinitionen, die von runden Klammern umschlossen ist. Jede Argumentdefinition ist angegeben als

```
ArgName:ArgType [= ArgDefaultValue]
```
 Für tabellarische Argumente hat *ArgType* dieselbe Syntax wie die Tabellendefinition (Klammern und eine Liste mit Spaltenname/Typ-Paaren), wobei zusätzlich ein einzelnes `(*)` unterstützt wird, das „beliebiges tabellarisches Schema“ angibt.

Zum Beispiel:

|Syntax                        |Beschreibung einer Eingabeargumentenliste                                 |
|------------------------------|-----------------------------------------------------------------|
|`()`                          |Keine Argumente|
|`(s:string)`                  |Einzelnes skalares Argument namens `s`, das einen Wert vom Typ `string` hat|
|`(a:long, b:bool=true)`       |Zwei skalare Argumente, wobei das zweite Argument einen Standardwert hat    |
|`(T1:(*), T2(r:real), b:bool)`|Drei Argumente (zwei tabellarische Argumente und ein Skalarargument)  |

> [!NOTE]
> Wenn Sie sowohl tabellarische Eingabeargumente als auch skalare Eingabeargumente verwenden, platzieren Sie alle tabellarischen Eingabeargumente vor den skalaren Eingabeargumenten.

## <a name="examples"></a>Beispiele

Eine Skalarfunktion:

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

Eine tabellarische Funktion, die keine Argumente hat:

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

Eine tabellarische Funktion, die sowohl eine tabellarische Eingabe als auch eine skalare Eingabe hat:

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

Eine tabellarische Funktion, die eine tabellarische Eingabe ohne Spaltenangabe verwendet.
An die Funktion kann eine beliebige Tabelle übergeben werden, und in der Funktion kann nicht auf Tabellenspalten verwiesen werden.

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

In der Deklaration einer benutzerdefinierten Funktion wird Folgendes bereitgestellt:

* **Name** der Funktion
* **Schema** der Funktion (von ihr akzeptierte Parameter, sofern vorhanden)
* **Rumpf** der Funktion

> [!Note]
> Überladen von Funktionen wird nicht unterstützt. Sie können nicht mehrere Funktionen mit demselben Namen und unterschiedlichen Eingabeschemas erstellen.

> [!TIP]
> Eine Lambdafunktion hat keinen Namen und wird über eine [let-Anweisung](../letstatement.md) an einen Namen gebunden. Daher kann sie als benutzerdefinierte gespeicherte Funktion angesehen werden.
> Beispiel: Deklaration für eine Lambdafunktion, die zwei Argumente akzeptiert (ein `string`-Argument namens `s` und ein `long`-Argument namens `i`). Die Funktion gibt (nachdem sie das erste Argument in eine Zahl umgewandelt hat) das Produkt des ersten und des zweiten Arguments zurück. Die Lambdafunktion wird an den Namen `f` gebunden:

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

Der **Rumpf** der Funktion enthält Folgendes:

* Genau einen Ausdruck, der den Rückgabewert der Funktion (Skalar- oder tabellarischer Wert) bereitstellt.
* Eine beliebige Anzahl (null oder mehr) von [let-Anweisungen](../letstatement.md), deren Gültigkeitsbereich dem des Funktionsrumpfs entspricht. Sind let-Anweisungen angegeben, müssen sie dem Ausdruck vorangestellt sein, der den Rückgabewert der Funktion definiert.
* Eine beliebige Anzahl (null oder mehr) von [Abfrageparameteranweisungen](../queryparametersstatement.md), in denen die Abfrageparameter deklariert sind, die von der Funktion verwendet werden. Sind solche Anweisungen angegeben, müssen sie dem Ausdruck vorangestellt sein, der den Rückgabewert der Funktion definiert.

> [!NOTE]
> Andere Arten von [Abfrageanweisungen](../statements.md), die auf der „obersten Ebene“ einer Abfrage unterstützt werden, werden innerhalb eines Funktionsrumpfs nicht unterstützt.

### <a name="examples-of-user-defined-functions"></a>Beispiele zu benutzerdefinierten Funktionen 

**Benutzerdefinierte Funktion, in denen eine let-Anweisung verwendet wird**

Im folgenden Beispiel wird der Name `Test` an eine benutzerdefinierte Funktion (Lambdafunktion) gebunden, in der drei let-Anweisungen verwendet werden. Die Ausgabe beträgt `70`.

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

**Benutzerdefinierte Funktion, in der ein Standardwert für einen Parameter definiert ist**

Das folgende Beispiel zeigt eine Funktion, die drei Argumente akzeptiert. Die beiden letzteren haben einen Standardwert und müssen im jeweiligen Aufruf nicht vorhanden sein.

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>Aufrufen einer benutzerdefinierten Funktion

Eine benutzerdefinierte Funktion, die keine Argumente hat, kann entweder über ihren Namen oder über ihren Namen und eine leere Argumentliste in Klammern aufgerufen werden.

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

Eine benutzerdefinierte Funktion, die mindestens ein skalares Argument hat, kann aufgerufen werden, indem der Tabellenname und eine konkrete Argumentliste in Klammern verwendet werden:

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

Eine benutzerdefinierte Funktion, die mindestens ein Tabellenargument (und eine beliebige Anzahl von skalaren Argumenten) hat, kann aufgerufen werden, indem der Tabellenname und eine konkrete Argumentliste in Klammern verwendet werden:

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

Sie können auch den Operator `invoke` verwenden, um eine benutzerdefinierte Funktion aufzurufen, die mindestens ein Tabellenargument hat und eine Tabelle zurückgibt. Diese Funktion ist nützlich, wenn das erste Tabellenargument der Funktion die Quelle des `invoke`-Operators ist:

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>Standardwerte

In Funktionen können unter den folgenden Bedingungen Standardwerte für einige ihrer Parameter bereitgestellt werden:

* Standardwerte können nur für Skalarparameter bereitgestellt werden.
* Standardwerte sind immer Literale (Konstanten). Sie können keine beliebigen Berechnungen sein.
* Ein Parameter ohne Standardwert muss immer vor Parametern stehen, die keinen Standardwert haben.
* In einem Aufruf müssen die Werte aller Parameter, die keinen Standardwert haben, in derselben Reihenfolge wie in der Funktionsdeklaration bereitgestellt werden.
* In einem Aufruf müssen die Werte für Parameter mit Standardwerten nicht bereitgestellt werden, dies kann jedoch erfolgen.
* In einem Aufruf können Argumente in einer Reihenfolge bereitgestellt werden, die nicht der Reihenfolge der Parameter entspricht. Ist dies der Fall, müssen die Namen der Argumente angegeben werden.

Im folgenden Beispiel wird eine Tabelle mit zwei identischen Datensätzen zurückgegeben. Im ersten Aufruf von `f` sind die Argumente vollständig „zusammengewürfelt“, sodass jedem explizit ein Name zugewiesen ist:

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
|12-b.default-7|
|12-b.default-7|

## <a name="view-functions"></a>view-Funktionen

Eine benutzerdefinierte Funktion, die keine Argumente hat und einen tabellarischen Ausdruck zurückgibt, kann als **view** gekennzeichnet werden. Markieren einer benutzerdefinierten Funktion als „view“ bedeutet, dass sich die Funktion wie eine Tabelle verhält, wenn die Platzhalterzeichen von Tabellennamen aufgelöst werden.
Das folgende Beispiel zeigt zwei benutzerdefinierte Funktionen, `T_view` und `T_notview`, und es zeigt, wie nur der erste durch den Platzhalterverweis in der `union` aufgelöst wird:

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="restrictions"></a>Beschränkungen

Es gelten folgende Einschränkungen:

* Benutzerdefinierte Funktionen können nicht als Aufrufinformationen für [toscalar()](../toscalarfunction.md) übergeben werden, die von dem Zeilenkontext abhängen, in dem die Funktion aufgerufen wird.
* Benutzerdefinierte Funktionen, die einen tabellarischen Ausdruck zurückgeben, können nicht mit einem Argument aufgerufen werden, das sich mit dem Zeilenkontext ändert.
* Eine Funktion, die mindestens eine tabellarische Eingabe hat, kann in einem Remotecluster nicht aufgerufen werden.
* Eine skalare Funktion kann nicht in einem Remotecluster aufgerufen werden.

Der einzige Ausnahme, in der eine benutzerdefinierte Funktion mit einem Argument aufgerufen werden kann, das sich mit dem Zeilenkontext ändert, liegt vor, wenn die benutzerdefinierte Funktion nur aus skalaren Funktionen besteht und `toscalar()` nicht in ihr verwendet wird.

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

## <a name="features-that-are-currently-unsupported-by-user-defined-functions"></a>Features, die derzeit für benutzerdefinierte Funktionen nicht unterstützt werden

Aus Gründen der Vollständigkeit finden Sie hier einige häufig benötigte Features für benutzerdefinierte Funktionen, die derzeit nicht unterstützt werden:

1.  Funktionsüberladung: Aktuell gibt es keine Möglichkeit, eine Funktion zu überladen (d. h., mehrere Funktionen mit demselben Namen und einem anderen Eingabeschema zu erstellen).

2.  Standardwerte: Der Standardwert für einen Skalarparameter einer Funktion muss ein skalares Literal (Konstante) sein. Außerdem können gespeicherte Funktionen keinen Standardwert des Typs `dynamic` haben.
