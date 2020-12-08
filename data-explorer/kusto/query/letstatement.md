---
title: 'Let-Anweisung: Azure Data Explorer'
description: In diesem Artikel wird die Let-Anweisung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.localizationpriority: high
ms.openlocfilehash: c102637adfa1fd0340d28a67b52354956b511ada
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513311"
---
# <a name="let-statement"></a>let-Anweisung

Let-Anweisungen binden Namen an Ausdrücke. Im restlichen Gültigkeitsbereich, in dem die let-Anweisung vorkommt, kann der Name verwendet werden, um auf seinen gebundenen Wert zu verweisen. Die let-Anweisung kann sich in einem globalen Gültigkeitsbereich oder einem Funktionstextbereich befinden.
Wenn dieser Name zuvor an einen anderen Wert gebunden war, wird die „innerste“ let-Anweisungsbindung verwendet.

Let-Anweisungen verbessern die Modularität und Wiederverwendbarkeit, da sie einen potenziell komplexen Ausdruck in mehrere Teile zerlegen können.
Jeder Teil ist über die let-Anweisung an einen Namen gebunden, und zusammen bilden sie das Ganze. Sie können auch verwendet werden, um benutzerdefinierte Funktionen und Sichten zu erstellen. Die Sichten sind Ausdrücke für Tabellen, deren Ergebnisse wie eine neue Tabelle aussehen.

> [!NOTE]
> Durch let-Anweisungen gebundene Namen müssen gültige Entitätsnamen sein.

Die folgenden durch let-Anweisungen gebundenen Ausdrücke sind möglich:
* Skalare Typen
* Tabellarische Typen
* Benutzerdefinierte Funktionen (Lambdas)

## <a name="syntax"></a>Syntax

`let` *Name* `=` *ScalarExpression* | *TabularExpression* | *FunctionDefinitionExpression*

|Feld  |Definition  |Beispiel  |
|---------|---------|---------|
|*Name*   | Der zu bindende Name. Der Name muss ein gültiger Entitätsname sein.    |Escapezeichen für den Entitätsnamen (z. B. `["Name with spaces"]`) sind zulässig.      |
|*ScalarExpression*     |  Ein Ausdruck mit einem Skalarergebnis, dessen Wert an den Namen gebunden ist.  | `let one=1;`  |
|*TabularExpression*    | Ein Ausdruck mit einem tabellarischen Ergebnis, dessen Wert an den Namen gebunden ist.   | `Logs | where Timestamp > ago(1h)`    |
|*FunctionDefinitionExpression*   | Ein Ausdruck, der ein Lambda ergibt, eine anonyme Funktionsdeklaration, die an den Namen gebunden werden soll.   |  `let f=(a:int, b:string) { strcat(b, ":", a) }`  |


### <a name="lambda-expressions-syntax"></a>Syntax von Lambdaausdrücken

[`view`] `(`[*TabularArguments*][`,`][*ScalarArguments*]`)` `{` *FunctionBody* `}`

`TabularArguments` - [*TabularArgName* `:` `(`[*AtrName* `:` *AtrType*] [`,` ... ]`)`] [`,` ... ][`,`]

 oder:

 [*TabularArgName* `:` `(` `*` `)`]

`ScalarArguments` - [*ArgName* `:` *ArgType*] [`,` ... ]


|Feld  |Definition  |Beispiel  |
|---------|---------|---------|
| **view** | Kann nur in einem parameterlosen Lambdaausdruck angezeigt werden, der keine Argumente aufweist. Gibt an, dass der gebundene Name eingeschlossen wird, wenn „alle Tabellen“ Abfragen sind. | Beispielsweise bei der Verwendung von `union *`.|
| ***TabularArguments** _ | Die Liste der formalen tabellarischen Ausdrucksargumente. 
| Jedes tabellarische Argument verfügt über Folgendes:||
|<ul><li> _TabularArgName*</li></ul> | Der Name des formalen tabellarischen Arguments. Der Name wird möglicherweise im *FunctionBody* angezeigt und ist an einen bestimmten Wert gebunden, wenn das Lambda aufgerufen wird. ||
|<ul><li>Tabellenschemadefinition </li></ul> | Eine Liste von Attributen mit ihren Typen| AtrName: AtrType|
| ***ScalarArguments** _ | Die Liste der formalen Skalarargumente. 
|Jedes Skalarargument verfügt über Folgendes:||
|<ul><li>_ArgName*</li></ul> | Der Name des formalen Skalararguments. Der Name wird möglicherweise im *FunctionBody* angezeigt und ist an einen bestimmten Wert gebunden, wenn das Lambda aufgerufen wird.  |
| <ul><li>*ArgType* </li></ul>| Der Typ des formalen Skalararguments. | Zurzeit werden nur die folgenden Typen als Lambdaargumenttyp unterstützt: `bool`, `string`, `long`, `datetime`, `timespan`, `real` und `dynamic` (und Aliase für diese Typen).

> [!NOTE]
>Der tabellarische Ausdruck, der im Lambdaaufruf verwendet wird, muss alle Attribute mit den übereinstimmenden Typen enthalten (ist jedoch nicht darauf beschränkt).
>
>`(*)` kann als tabellarischer Ausdruck verwendet werden. 
>
> Jeder tabellarische Ausdruck kann im Lambdaaufruf verwendet werden, und auf keine seiner Spalten kann im Lambdaausdruck zugegriffen werden. 
>
> Alle tabellarischen Argumente sollten vor den Skalarargumenten auftreten.

## <a name="multiple-and-nested-let-statements"></a>Mehrere und geschachtelte let-Anweisungen

Mehrere let-Anweisungen können mit dem Trennzeichen Semikolon (`;`) zwischen ihnen verwendet werden, wie im folgenden Beispiel gezeigt.

> [!NOTE]
> Die letzte Anweisung muss ein gültiger Abfrageausdruck sein. 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

Geschachtelte let-Anweisungen sind zulässig und können innerhalb eines Lambdaausdrucks verwendet werden.
Let-Anweisungen und -Argumente sind im aktuellen und inneren Gültigkeitsbereich des Funktionstexts sichtbar.

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

## <a name="examples"></a>Beispiele

### <a name="use-let-function-to-define-constants"></a>Verwenden der Let-Funktion zum Definieren von Konstanten

Im folgenden Beispiel wird der Name `x` an das skalare Literal `1` gebunden und dann in einer tabellarischen Ausdrucksanweisung verwendet.

```kusto
let x = 1;
range y from x to x step x
```

Dieses Beispiel ist dem vorhergehenden ähnlich, nur der Name der let-Anweisung wird mit dem `['name']`-Begriff angegeben.

```kusto
let ['x'] = 1;
range y from x to x step x
```

### <a name="use-let-for-scalar-values"></a>Verwenden von let für Skalarwerte.

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="use-let-statement-with-arguments-for-scalar-calculation"></a>Verwenden der let-Anweisung mit Argumenten für die Skalarberechnung

Dieses Beispiel verwendet die let-Anweisung mit Argumenten für die Skalarberechnung. Die Abfrage definiert Funktion `MultiplyByN` für die Multiplikation von zwei Zahlen.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let MultiplyByN = (val:long, n:long) { val * n };
range x from 1 to 5 step 1 
| extend result = MultiplyByN(x, 5)
```

|x|result|
|---|---|
|1|5|
|2|10|
|3|15|
|4|20|
|5|25|

Im folgenden Beispiel werden führende/nachfolgende Einsen (`1`) aus der Eingabe entfernt.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let TrimOnes = (s:string) { trim("1", s) };
range x from 10 to 15 step 1 
| extend result = TrimOnes(tostring(x))
```

|x|result|
|---|---|
|10|0|
|11||
|12|2|
|13|3|
|14|4|
|15|5|


### <a name="use-multiple-let-statements"></a>Verwenden mehrerer let-Anweisungen

In diesem Beispiel werden zwei let-Anweisungen definiert, wobei eine Anweisung (`foo2`) eine andere Anweisung (`foo1`) verwendet.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="use-the-view-keyword-in-a-let-statement"></a>Verwenden des `view`-Schlüsselworts in einer let-Anweisung

In diesem Beispiel wird gezeigt, wie Sie die let-Anweisung mit dem `view`-Schlüsselwort verwenden.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Range10 = view () { range MyColumn from 1 to 10 step 1 };
let Range20 = view () { range MyColumn from 1 to 20 step 1 };
search MyColumn == 5
```

|$table|MyColumn|
|---|---|
|Range10|5|
|Range20|5|


### <a name="use-materialize-function"></a>Verwenden der materialize-Funktion

Mit der [`materialize`](materializefunction.md)-Funktion können Sie Ergebnisse von Unterabfragen während der Zeit der Abfrageausführung zwischenspeichern. 

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
