---
title: 'Let-Anweisung: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt die Let-Anweisung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2e21f0b41f34b469e409109a2586f3e5fd98fa5
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271467"
---
# <a name="let-statement"></a>let-Anweisung

Let-Anweisungen binden Namen an Ausdrücke. Für den Rest des Bereichs, in dem die Let-Anweisung angezeigt wird (globaler Gültigkeitsbereich oder in einem Funktions Rumpfbereich), kann der Name verwendet werden, um auf seinen gebundenen Wert zu verweisen. Wenn dieser Name zuvor an einen anderen Wert gebunden war, wird die "innerste" Let-Anweisungs Bindung verwendet.

Let-Anweisungen verbessern die Modularität und die Wiederverwendung, da Sie einen potenziell komplexen Ausdruck in mehrere Teile unterteilen können, die jeweils an einen Namen durch die Let-Anweisung gebunden sind, und zusammengesetzt werden. Sie können auch verwendet werden, um benutzerdefinierte Funktionen und Sichten zu erstellen (Ausdrücke über Tabellen, deren Ergebnisse wie eine neue Tabelle aussehen).

Durch Let-Anweisungen gebundene Namen müssen gültige Entitäts Namen sein.

Von Let-Anweisungen gebundene Ausdrücke können folgende sein:
* Von skalaren Typen
* Tabellarischer Typ
* Benutzerdefinierte Funktionen (Lambdas)

**Syntax**

`let`*Name* `=` *Scalarexpression*  |  *Tabularexpression*  |  *FunctionDefinitionExpression*

* *Name*: der Name, der gebunden werden soll. Der Name muss ein gültiger Entitäts Name sein, und Entitäts Name Escapezeichen (z. b. `["Name with spaces"]` ) ist zulässig. 
* *Scalarexpression*: ein Ausdruck mit einem skalaren Ergebnis, dessen Wert an den Namen gebunden wird. Beispiel: `let one=1;`.
* *Tabularexpression*: ein Ausdruck mit einem Tabellen Ergebnis, dessen Wert an den Namen gebunden wird. Beispiel: `Logs | where Timestamp > ago(1h)`.
* *FunctionDefinitionExpression*: ein Ausdruck, der ein Lambda Ergebnis (eine anonyme Funktionsdeklaration) ergibt, das an den Namen gebunden werden soll.
  Beispiel: `let f=(a:int, b:string) { strcat(b, ":", a) }`.

Lambda-Ausdrücke haben die folgende Syntax:

[ `view` ] `(` [*Tabulararguments*] [ `,` ] [*scalararguments*] `)` `{` *functionbody*`}`

`TabularArguments`-[*Tabularargname* `:` `(` [*atrname* `:` *atrtype*] [ `,` ...] `)` ] [`,` ... ] [`,`]

 oder:-[*tabularargname* `:` `(` `*` `)` ]

`ScalarArguments`-[*Argname* `:` *argtype*] [ `,` ...]

* `view`kann nur in einem Parameter losen Lambda-Ausdruck (einer, der keine Argumente aufweist) angezeigt werden und gibt an, dass der gebundene Name eingeschlossen wird, wenn "alle Tabellen" Abfragen sind (z. b. bei Verwendung von `union *` ).
* *Tabulararguments* ist die Liste der formalen Tabellen Ausdrucks Argumente.
  Jedes Argument hat Folgendes:
  * *Tabularargname* : der Name des formalen tabellarischen Arguments. Der Name kann dann in *functionbody* angezeigt werden und wird an einen bestimmten Wert gebunden, wenn der Lambda-Ausdruck aufgerufen wird. 
  * Tabellen Schema Definition: eine Liste von Attributen mit ihren Typen (atrname: atrtype).
  Der tabellarische Ausdruck, der im Lambda Aufruf verwendet wird, muss über alle diese Attribute mit den übereinstimmenden Typen verfügen, ist jedoch nicht darauf beschränkt. 
  ' (*) ' kann als tabellarischer Ausdruck verwendet werden. In diesem Fall kann jeder tabellarische Ausdruck im Lambda Aufruf verwendet werden, und auf keine seiner Spalten kann im Lambda-Ausdruck zugegriffen werden.
  Alle tabellarischen Argumente sollten vor den skalaren Argumenten angezeigt werden.
* *Scalararguments* ist die Liste der formalen skalaren Argumente. 
  Jedes Argument hat Folgendes:
  * *Argname* : der Name des formalen skalaren Arguments. Der Name kann dann in *functionbody* angezeigt werden und wird an einen bestimmten Wert gebunden, wenn der Lambda-Ausdruck aufgerufen wird.  
  * *Argtype* : der Typ des formalen skalaren Arguments. Zurzeit werden nur die folgenden Typen als Lambda-Argumenttyp unterstützt: `bool` , `string` , `long` , `datetime` , `timespan` , `real` und `dynamic` (und Aliase zu diesen Typen).

**Multiple-und netsted Let-Anweisungen**

Mehrere Let-Anweisungen können mit Trennzeichen dazwischen verwendet werden, `;` wie im folgenden Beispiel gezeigt.
Die letzte Anweisung muss ein gültiger Abfrage Ausdruck sein: 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

Geschachtelte Let-Anweisungen sind zulässig und können innerhalb eines Lambda-Ausdrucks verwendet werden.
Let-Anweisungen und Argumente sind im aktuellen und inneren Gültigkeitsbereich des Funktions Texts sichtbar.

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

**Beispiele**

### <a name="using-let-to-define-constants"></a>Verwenden von Let zum Definieren von Konstanten

Im folgenden Beispiel wird der Name `x` an das skalare Literale gebunden `1` und dann in einer tabellarischen Ausdrucks Anweisung verwendet:

```kusto
let x = 1;
range y from x to x step x
```

Gleiches Beispiel, aber in diesem Fall: der Name der Let-Anweisung wird mit dem folgenden `['name']` Begriff angegeben:

```kusto
let ['x'] = 1;
range y from x to x step x
```

Ein weiteres Beispiel, in dem Let für skalare Werte verwendet wird:

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="using-multiple-let-statements"></a>Verwenden von mehreren Let-Anweisungen

Im folgenden Beispiel werden zwei Let-Anweisungen definiert, wobei eine-Anweisung ( `foo2` ) einen anderen verwendet ( `foo1` ).

```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="using-materialize-function"></a>Verwenden der materialisieren-Funktion

[`materialize`](materializefunction.md)die Funktion ermöglicht das Zwischenspeichern von Unterabfrage Ergebnissen während der Abfrage Ausführung. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let totalPagesPerDay = PageViews
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
let materializedScope = PageViews
| summarize by Page, Day = startofday(Timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project Page, Day1 = Day
| join kind = inner
(
    cachedResult
    | project Page, Day2 = Day
)
on Page
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1


```

|Tag1|Tag2|Prozentsatz|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|
