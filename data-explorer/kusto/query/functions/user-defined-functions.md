---
title: Benutzerdefinierte Funktionen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden benutzerdefinierte Funktionen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e8372be303b1540e1421f226ed0fd0fe74d44545
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610219"
---
# <a name="user-defined-functions"></a>Benutzerdefinierte Funktionen

Kusto unterstützt benutzerdefinierte Funktionen, die entweder:
* Teil der Abfrage selbst (**Ad-hoc-Funktionen**) 
* Persistent als Teil der Datenbankmetadaten gespeichert (**gespeicherte Funktionen**)

Eine benutzerdefinierte Funktion hat:
* Ein Name:
    * Muss den [Idbezeichnerbenennungsregeln](../schema-entities/entity-names.md#identifier-naming-rules) folgen
    * Muss im Rahmen der Definition mit einer Typspezifikation eindeutig sein
* Eine stark typisierte Liste von Eingabeparametern:
    * Kann skalare oder tabellarische Ausdrücke sein
    * Skalare Parameter können mit einem Standardwert bereitgestellt werden, der implizit verwendet wird, wenn der Aufrufer der Funktion keinen Wert für den Parameter liefert (siehe [Standardwerte](#default-values), unten)
* Ein stark typisierter Rückgabewert, der skalar oder tabellarisch sein kann

Die Ein- und Ausgänge einer Funktion bestimmen, wie und wo sie verwendet werden kann:

* **Eine Skalarfunktion**: 
    * Ist eine Funktion ohne Eingänge oder nur mit skalaren Eingängen, und das erzeugt einen skalaren Ausgang
    * Kann überall dort verwendet werden, wo ein Skalarausdruck zulässig ist
    * Kann nur den Zeilenkontext verwenden, in dem er definiert ist
    * Kann nur auf Tabellen (und Ansichten) verweisen, die sich im zugänglichen Schema befinden

Beispiel:

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

* **Eine tabellarische Funktion**: 
    * Ist eine Funktion ohne Eingänge oder mindestens ein tabellarischer Eingang und erzeugt einen tabellarischen Ausgang
    * Kann überall dort verwendet werden, wo ein tabellarischer Ausdruck zulässig ist 

> [!NOTE]
> Alle Tabellarischen Parameter müssen vor skalaren Parametern angezeigt werden.

Beispiel für eine tabellarische Funktion, die keine Argumente verwendet:

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

Beispiel für eine tabellarische Funktion, die einen tabellarischen Eingang und einen Skalareingang verwendet:

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

Beispiel für eine Tabellarische Funktion, die eine tabellarische Eingabe ohne Angabe von Spalten verwendet. Jede Tabelle kann an eine Funktion übergeben werden, und innerhalb der Funktion kann auf keine Tabellenspalte verwiesen werden.

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

## <a name="declaring-user-defined-functions"></a>Deklarieren benutzerdefinierter Funktionen

Die Deklaration einer benutzerdefinierten Funktion sieht Folgendes vor:

* **Funktionsname**
* **Funktionsschema** (Parameter, die es akzeptiert, falls vorhanden)
* **Funktionskörper**

> [!Note]
> Überladenvon Funktionen wird nicht unterstützt. Beispielsweise können Sie nicht mehrere Funktionen mit demselben Namen und unterschiedlichen Eingabeschemas erstellen.

> [!TIP]
> Lambda-Funktionen haben keinen Namen und sind mit einer [let-Anweisung](../letstatement.md)an einen Namen gebunden. Daher können sie als benutzerdefinierte gespeicherte Funktionen betrachtet werden.
> Beispiel: Deklaration für eine Lambda-Funktion, die zwei Argumente akzeptiert (eine `string` aufgerufene `s` und eine `long` aufgerufene `i`). Es gibt das Produkt des ersten (nach der Umwandlung in eine Zahl) und das zweite zurück. Der Lambda ist an `f`den Namen gebunden:

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

Der **Funktionstext** umfasst:

* Genau ein Ausdruck, der den Rückgabewert der Funktion bereitstellt (Skalar- oder Tabellarisches Wert).
* Jede Beliebige Zahl (null oder mehr) von [let-Anweisungen](../letstatement.md), deren Bereich der des Funktionstexts ist. Wenn angegeben, müssen die let-Anweisungen dem Ausdruck vorangehen, der den Rückgabewert der Funktion definiert.
* Jede Beliebige Anzahl (null oder mehr) von [Abfrageparameteranweisungen](../queryparametersstatement.md), die Abfrageparameter deklarieren, die von der Funktion verwendet werden. Wenn angegeben, müssen sie dem Ausdruck vorangehen, der den Rückgabewert der Funktion definiert.

> [!NOTE]
> Andere Arten von [Abfrageanweisungen,](../statements.md) die auf der Abfrage "top level" unterstützt werden, werden in einem Funktionstext nicht unterstützt.

### <a name="examples-of-user-defined-functions"></a>Beispiele für benutzerdefinierte Funktionen 

**Benutzerdefinierte Funktion, die eine let-Anweisung verwendet**

Im folgenden Beispiel wird `Test` der Name an eine benutzerdefinierte Funktion (Lambda) gebunden, die drei let-Anweisungen verwendet. Die Ausgabe `70`ist:

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

**Benutzerdefinierte Funktion, die einen Standardwert für einen Parameter definiert**

Das folgende Beispiel zeigt eine Funktion, die drei Argumente akzeptiert. Die beiden letztgenannten haben einen Standardwert und müssen nicht an der Aufrufsite vorhanden sein.

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>Aufrufen einer benutzerdefinierten Funktion

Eine benutzerdefinierte Funktion, die keine Argumente annimmt, kann durch ihren Namen oder durch ihren Namen mit einer leeren Argumentliste in Klammern aufgerufen werden.

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

Eine benutzerdefinierte Funktion, die ein oder mehrere skalare Argumente verwendet, kann mithilfe des Tabellennamens und einer konkreten Argumentliste in Klammern aufgerufen werden:

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

Eine benutzerdefinierte Funktion, die ein oder mehrere Tabellenargumente (und eine beliebige Anzahl von skalaren Argumenten) verwendet, kann mithilfe des Tabellennamens und einer konkreten Argumentliste in Klammern aufgerufen werden:

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

Sie können den `invoke` Operator auch verwenden, um eine benutzerdefinierte Funktion aufzurufen, die ein oder mehrere Tabellenargumente verwendet und eine Tabelle zurückgibt. Dies ist nützlich, wenn das erste konkrete Tabellenargument für die Funktion die Quelle des `invoke` Operators ist:

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>Standardwerte

Funktionen können Standardwerte für einige ihrer Parameter unter den folgenden Bedingungen bereitstellen:

* Standardwerte können nur für skalare Parameter bereitgestellt werden.
* Standardwerte sind immer Literale (Konstanten). Sie können keine willkürlichen Berechnungen sein.
* Parameter ohne Standardwert gehen immer vor Parametern, die einen Standardwert aufweisen.
* Aufrufer müssen den Wert aller Parameter ohne Standardwerte bereitstellen, die in der gleichen Reihenfolge wie die Funktionsdeklaration angeordnet sind.
* Aufrufer müssen den Wert für Parameter mit Standardwerten nicht angeben, können dies jedoch tun.
* Aufrufer können Argumente in einer Reihenfolge bereitstellen, die nicht mit der Reihenfolge der Parameter übereinstimmt. Wenn ja, müssen sie ihre Argumente benennen.

Im folgenden Beispiel wird eine Tabelle mit zwei identischen Datensätzen zurückgegeben. Beim ersten Aufruf `f`von werden die Argumente vollständig "verschlüsselt", so dass jedes explizit einen Namen erhält:

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

## <a name="view-functions"></a>Funktionen anzeigen

Eine benutzerdefinierte Funktion, die keine Argumente annimmt und einen tabellenförmigen Ausdruck zurückgibt, kann als **Ansicht**markiert werden. Das Markieren einer benutzerdefinierten Funktion als Ansicht bedeutet, dass sich die Funktion bei jeder Auflösung des Platzhaltertabellennamens wie eine Tabelle verhält.
Das folgende Beispiel zeigt zwei `T_view` benutzerdefinierte Funktionen und `T_notview`zeigt, wie nur die erste `union`durch den Platzhalterverweis in der aufgelöst wird:

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="user-defined-functions-usage-restrictions"></a>Benutzerdefinierte Funktionsnutzungseinschränkungen

Es gelten folgende Einschränkungen:

* Benutzerdefinierte Funktionen können nicht in [toscalar()-Aufrufinformationen](../toscalarfunction.md) übergeben werden, die vom Zeilenkontext abhängen, in dem die Funktion aufgerufen wird.
* Benutzerdefinierte Funktionen, die einen tabellarischen Ausdruck zurückgeben, können nicht mit einem Argument aufgerufen werden, das mit dem Zeilenkontext variiert.
* Eine Funktion, die mindestens eine tabellarische Eingabe verwendet, kann auf einem Remotecluster nicht aufgerufen werden.
* Eine Skalarfunktion kann auf einem Remotecluster nicht aufgerufen werden.

Der einzige Ort, an dem eine benutzerdefinierte Funktion mit einem Argument aufgerufen werden kann, das mit dem Zeilenkontext variiert, ist, wenn die benutzerdefinierte Funktion nur aus skalaren Funktionen besteht und nicht verwendet. `toscalar()`

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
