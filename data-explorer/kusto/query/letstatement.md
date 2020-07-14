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
ms.openlocfilehash: 2994a65e8726edaba22c6905290b4b69660e0586
ms.sourcegitcommit: 284152eba9ee52e06d710cc13200a80e9cbd0a8b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2020
ms.locfileid: "86291541"
---
# <a name="let-statement"></a>let-Anweisung

Let-Anweisungen binden Namen an Ausdrücke. Für den Rest des Bereichs, in dem die Let-Anweisung angezeigt wird, kann der Name verwendet werden, um auf seinen gebundenen Wert zu verweisen. Die Let-Anweisung kann sich in einem globalen Gültigkeitsbereich oder einem Funktions Textbereich befinden.
Wenn dieser Name zuvor an einen anderen Wert gebunden war, wird die "innerste" Let-Anweisungs Bindung verwendet.

Let-Anweisungen verbessern die Modularität und Wiederverwendung, da Sie einen potenziell komplexen Ausdruck in mehrere Teile zerlegen können.
Jeder Teil ist über die Let-Anweisung an einen Namen gebunden, und zusammen bilden Sie den gesamten. Sie können auch verwendet werden, um benutzerdefinierte Funktionen und Ansichten zu erstellen. Die Ansichten sind Ausdrücke über Tabellen, deren Ergebnisse wie eine neue Tabelle aussehen.

> [!NOTE]
> Durch Let-Anweisungen gebundene Namen müssen gültige Entitäts Namen sein.

Von Let-Anweisungen gebundene Ausdrücke können folgende sein:
* Skalare Typen
* Tabellarische Typen
* Benutzerdefinierte Funktionen (Lambdas)

## <a name="syntax"></a>Syntax

`let`*Name* `=` *Scalarexpression*  |  *Tabularexpression*  |  *FunctionDefinitionExpression*

|Feld  |Definition  |Beispiel  |
|---------|---------|---------|
|*Name*   | Der zu bindende Name. Der Name muss ein gültiger Entitäts Name sein.    |Das Escapezeichen des Entitäts namens `["Name with spaces"]` ist zulässig.      |
|*Scalarexpression*     |  Ein Ausdruck mit einem skalaren Ergebnis, dessen Wert an den Namen gebunden ist.  | `let one=1;`  |
|*Tabularexpression*    | Ein Ausdruck mit einem Tabellen Ergebnis, dessen Wert an den Namen gebunden ist.   | `Logs | where Timestamp > ago(1h)`    |
|*FunctionDefinitionExpression*   | Ein Ausdruck, der ein Lambda Ergebnis ergibt, eine anonyme Funktionsdeklaration, die an den Namen gebunden werden soll.   |  `let f=(a:int, b:string) { strcat(b, ":", a) }`  |


### <a name="lambda-expressions-syntax"></a>Syntax von Lambda Ausdrücken

[ `view` ] `(` [*Tabulararguments*] [ `,` ] [*scalararguments*] `)` `{` *functionbody*`}`

`TabularArguments`-[*Tabularargname* `:` `(` [*atrname* `:` *atrtype*] [ `,` ...] `)` ] [`,` ... ] [`,`]

 oder:

 [*Tabularargname* `:` `(` `*` `)`]

`ScalarArguments`-[*Argname* `:` *argtype*] [ `,` ...]


|Feld  |Definition  |Beispiel  |
|---------|---------|---------|
| **view** | Kann nur in einem Parameter losen Lambda-Ausdruck angezeigt werden, der keine Argumente aufweist. Gibt an, dass der gebundene Name eingeschlossen wird, wenn "alle Tabellen" Abfragen sind. | Beispielsweise bei Verwendung von `union *` .|
| ***Tabulararguments*** | Die Liste der formalen Tabellen Ausdrucks Argumente. 
| Jedes tabellarische Argument hat Folgendes:||
|<ul><li> *Tabularargname*</li></ul> | Der Name des formalen tabellarischen Arguments. Der Name wird möglicherweise im *functionbody* -Ausdruck angezeigt und ist an einen bestimmten Wert gebunden, wenn der Lambda-Ausdruck aufgerufen wird. ||
|<ul><li>Tabellen Schema Definition </li></ul> | Eine Liste von Attributen mit ihren Typen| Atrname: atrtype|
| ***Scalararguments*** | Die Liste der formalen skalaren Argumente. 
|Jedes skalare Argument hat Folgendes:||
|<ul><li>*Argname*</li></ul> | Der Name des formalen skalaren Arguments. Der Name wird möglicherweise im *functionbody* -Ausdruck angezeigt und ist an einen bestimmten Wert gebunden, wenn der Lambda-Ausdruck aufgerufen wird.  |
| <ul><li>*Argtype* </li></ul>| Der Typ des formalen skalaren Arguments. | Zurzeit werden nur die folgenden Typen als Lambda-Argumenttyp unterstützt: `bool` , `string` , `long` , `datetime` , `timespan` , `real` und `dynamic` (und Aliase zu diesen Typen).

> [!NOTE]
>Der tabellarische Ausdruck, der im Lambda Aufruf verwendet wird, muss alle Attribute mit den übereinstimmenden Typen enthalten (ist jedoch nicht darauf beschränkt).
>
>`(*)`kann als tabellarischer Ausdruck verwendet werden. 
>
> Jeder tabellarische Ausdruck kann im Lambda Aufruf verwendet werden, und auf keine seiner Spalten kann im Lambda-Ausdruck zugegriffen werden. 
>
> Alle tabellarischen Argumente sollten vor den skalaren Argumenten angezeigt werden.

## <a name="multiple-and-nested-let-statements"></a>Multiple-und netsted Let-Anweisungen

Mehrere Let-Anweisungen können mit dem Semikolon, dem Trennzeichen dazwischen verwendet werden `;` , wie im folgenden Beispiel gezeigt.

> [!NOTE]
> Die letzte Anweisung muss ein gültiger Abfrage Ausdruck sein. 

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

## <a name="examples"></a>Beispiele

### <a name="use-let-function-to-define-constants"></a>Verwenden der Let-Funktion zum Definieren von Konstanten

Im folgenden Beispiel wird der Name `x` an das skalare Literale gebunden `1` und dann in einer tabellarischen Ausdrucks Anweisung verwendet.

```kusto
let x = 1;
range y from x to x step x
```

Dieses Beispiel ähnelt dem vorherigen, nur der Name der Let-Anweisung wird mithilfe des- `['name']` Begriffs angegeben.

```kusto
let ['x'] = 1;
range y from x to x step x
```

### <a name="use-let-for-scalar-values"></a>Verwenden Sie Let für skalare Werte.

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="use-multiple-let-statements"></a>Verwenden mehrerer Let-Anweisungen

In diesem Beispiel werden zwei Let-Anweisungen definiert, bei denen eine Anweisung ( `foo2` ) einen anderen verwendet ( `foo1` ).

```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="use-materialize-function"></a>Verwenden der materialisieren-Funktion

Mit der- [`materialize`](materializefunction.md) Funktion können Sie Unterabfrage Ergebnisse während der Abfrage Ausführung zwischenspeichern. 

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

|Tag1|Tag2|Prozentwert|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|
