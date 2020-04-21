---
title: Let-Anweisung - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Let-Anweisung in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 890a6e21400048031e4ebd3df9749b6c47803e71
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81524445"
---
# <a name="let-statement"></a>let-Anweisung

Lassen Sie Anweisungen Namen an Ausdrücke binden. Für den Rest des Bereichs, in dem die let-Anweisung angezeigt wird (globaler Bereich oder in einem Funktionstextbereich), kann der Name verwendet werden, um auf seinen gebundenen Wert zu verweisen. Wenn dieser Name zuvor an einen anderen Wert gebunden war, wird die "innerste" let-Anweisungsbindung verwendet.

Lassen Sie Anweisungen die Modularität und Wiederverwendung verbessern, da sie es ermöglichen, einen potenziell komplexen Ausdruck in mehrere Teile aufzuteilen, die jeweils durch die let-Anweisung an einen Namen gebunden sind, und sie zusammen zu komponieren. Sie können auch zum Erstellen benutzerdefinierter Funktionen und Ansichten verwendet werden (Ausdrücke über Tabellen, deren Ergebnisse wie eine neue Tabelle aussehen).

Namen, die an let-Anweisungen gebunden sind, müssen gültige Entitätsnamen sein.

Ausdrücke, die an let-Anweisungen gebunden sind, können wie folgt sein:
* Von skalarer Typ
* Von tabellarischem Typ
* Benutzerdefinierte Funktionen (Lambdas)

**Syntax**

`let`*Name* `=` *ScalarExpression* | *TabularExpression* | *FunctionDefinitionExpression*

* *Name*: Der name, der gebunden werden soll. Der Name muss ein gültiger Entitätsname sein, und die `["Name with spaces"]`Flucht des Entitätsnamens (z. B. ) ist zulässig. 
* *ScalarExpression*: Ein Ausdruck mit einem skalaren Ergebnis, dessen Wert an den Namen gebunden wird. Beispiel: `let one=1;`.
* *TabularExpression*: Ein Ausdruck mit einem tabellarischen Ergebnis, dessen Wert an den Namen gebunden wird. Beispiel: `Logs | where Timestamp > ago(1h)`.
* *FunctionDefinitionExpression*: Ein Ausdruck, der einen Lambda (eine anonyme Funktionsdeklaration) ergibt, der an den Namen gebunden werden soll.
  Beispiel: `let f=(a:int, b:string) { strcat(b, ":", a) }`.

Lambda-Ausdrücke haben die folgende Syntax:

[`view` `(`] [*TabularArgumente*`,`][ ][`)` `{` *ScalarArguments*] *FunctionBody*`}`

`TabularArguments`- [*TabularArgName* `:` `(`[*AtrName* `:` `,` *AtrType*] [ ... ] `)`] [`,` ... ] [`,`]

 oder: - [*TabularArgName* `:` `(` `*` `)`]

`ScalarArguments`- [*ArgName* `:` *ArgType*] [`,` ... ]

* `view`kann nur in einem parameterlosen Lambda angezeigt werden (einer, der keine Argumente hat) und gibt an, `union *`dass der gebundene Name eingeschlossen wird, wenn "alle Tabellen" Abfragen sind (z. B. bei Verwendung ).
* *TabularArguments* sind die Liste der formalen Tabellenausdrucksargumente.
  Jedes Argument hat:
  * *TabularArgName* - der Name des formalen tabellarischen Arguments. Der Name kann dann im *FunctionBody* angezeigt werden und ist an einen bestimmten Wert gebunden, wenn der Lambda aufgerufen wird. 
  * Tabellenschemadefinition - eine Liste von Attributen mit ihren Typen (AtrName : AtrType).
  Der tabellarische Ausdruck, der in der Lambda-Aufrufierung verwendet wird, muss alle diese Attribute mit den übereinstimmenden Typen aufweisen, ist jedoch nicht auf sie beschränkt. 
  '(*)' kann als tabellarischer Ausdruck verwendet werden. In diesem Fall kann jeder tabellarische Ausdruck in der Lambda-Aufrufierung verwendet werden, und auf keine seiner Spalten kann im Lambda-Ausdruck zugegriffen werden.
  Alle tabellarischen Argumente sollten vor den Skalarargumenten angezeigt werden.
* *ScalarArguments* sind die Liste der formalen Skalarargumente. 
  Jedes Argument hat:
  * *ArgName* - der Name des formalen skalaren Arguments. Der Name kann dann im *FunctionBody* angezeigt werden und ist an einen bestimmten Wert gebunden, wenn der Lambda aufgerufen wird.  
  * *ArgType* - der Typ des formalen Skalararguments. Derzeit werden nur die folgenden Typen als `bool`Lambda-Argumenttyp unterstützt: `string`, , `long` `datetime`, `timespan`, `real`, und `dynamic` (und Aliasnamen für diese Typen).

**Mehrere und verschachtelte let-Anweisungen**

Mehrere let-Anweisungen können `;` mit einem Trennzeichen zwischen ihnen verwendet werden, wie im folgenden Beispiel gezeigt.
Die letzte Anweisung muss ein gültiger Abfrageausdruck sein: 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

Geschachtelte let-Anweisungen sind zulässig und können in einem Lambda-Ausdruck verwendet werden.
Lassen Sie Anweisungen und Argumente im aktuellen und inneren Bereich des Funktionstexts sichtbar werden.

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

**Beispiele**

### <a name="using-let-to-define-constants"></a>Verwenden von let zum Definieren von Konstanten

Das folgende Beispiel bindet `x` den Namen an `1`das skalare Literal und verwendet ihn dann in einer tabellenförmigen Ausdrucksanweisung:

```kusto
let x = 1;
range y from x to x step x
```

Das gleiche Beispiel, aber in diesem Fall - `['name']` der Name der let-Anweisung wird mit Begriff gegeben:

```kusto
let ['x'] = 1;
range y from x to x step x
```

Ein weiteres Beispiel, das let für skalare Werte verwendet:

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="using-multiple-let-statements"></a>Verwenden mehrerer let-Anweisungen

Im folgenden Beispiel werden zwei let-Anweisungen definiert, bei denen eine Anweisung (`foo2`) eine andere verwendet (`foo1`).

```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="using-materialize-function"></a>Verwenden der Materialisierungsfunktion

[`materialize`](materializefunction.md)Funktion ermöglicht das Zwischenspeichern von Unterabfrageergebnissen während der Zeit der Abfrageausführung. 

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
