---
title: musteranweisung - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Musteranweisung in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d7ad021fe7b458d54beeb24b908420324b45ad39
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765667"
---
# <a name="pattern-statement"></a>Musteranweisung

::: zone pivot="azuredataexplorer"

Ein **Muster** ist ein benanntes ansichtsähnliches Konstrukt, das vordefinierte Zeichenfolgen-Tupeln parameterlosen Funktionskörpern zuordnet. Muster sind in zwei Aspekten einzigartig:

* Muster werden mithilfe einer Syntax " aufgerufen, die abschnittsgebundenen Tabellenverweisen ähnelt.
* Muster verfügen über einen gesteuerten, engen Satz von Argumentwerten, die zugeordnet werden können, und der Zuordnungsprozess wird von Kusto ausgeführt. Dies bedeutet, wenn ein Muster deklariert, aber nicht definiert ist, identifiziert und kennzeichnet Kusto alle Aufrufe zum Muster als Fehler, wodurch es möglich ist, diese Muster durch eine Anwendung der mittleren Ebene zu "lösen".


## <a name="pattern-declaration"></a>Musterdeklaration
Die pattern-Anweisung wird verwendet, um ein Muster zu deklarieren oder zu definieren.
Im Folgenden ist z. B. `app` eine Musteranweisung, die als Muster deklariert wird:

```kusto
declare pattern app;
```

Diese Aussage sagt `app` Kusto, dass es sich um ein Muster handelt, aber nicht, wie das Muster aufgelöst werden soll. Daher führt jeder Versuch, dieses Muster in der Abfrage aufzurufen, zu einem bestimmten Fehler, der alle diese Aufrufe auflistet. Beispiel:

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

Diese Abfrage generiert einen Fehler aus Kusto, der angibt, dass `app("ApplicationX")["StartEvents"]` `app("ApplicationX")["StopEvents"]`die folgenden Musteraufrufe nicht aufgelöst werden können: und .

**Syntax**

`declare``pattern` *PatternName*

## <a name="pattern-definition"></a>Musterdefinition

Die musteranweisung kann auch verwendet werden, um ein Muster zu definieren. In einer Musterdefinition werden alle möglichen Aufrufe des Musters explizit festgelegt und der entsprechende tabellarische Ausdruck angegeben. Wenn Kusto dann die Abfrage ausführt, ersetzt es jeden Musteraufruf durch den entsprechenden Mustertext. Beispiel:

```kusto
declare pattern app = (applicationId:string)[eventType:string]
{
    ("ApplicationX").["StopEvents"] = { database("AppX").Events | where EventType == "StopEvent" };
    ("ApplicationX").["StartEvents"] = { database(applicationId).Events | where EventType == eventType } ;
};
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

Der Ausdruck, der für jedes übereinstimmende Muster bereitgestellt wird, ist entweder ein Tabellenname oder ein Verweis auf eine [let-Anweisung](letstatement.md).

**Syntax**

`declare``pattern` *PatternName* = *ArgName* `:` *ArgType* `,` ArgName ArgType [ ... ]`(` `)` [`[` *PathName* `:` *PathArgType* `]`]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* `,` [ *ArgValue2* ... ] `)` [ `.[` `]` *PathValue `=` `{` *expression* `};` &nbsp; &nbsp; &nbsp; ] Ausdruck &nbsp; [ &nbsp; `,` *ArgValue2_2* ArgValue1_2 [ ArgValue2_2 ... ] *ArgValue1_2* &nbsp; &nbsp; &nbsp; `(` `)` [ `.[` *PathValue_2* `]` PathValue_2 `=` `{`] *expression_2* `};` expression_2 &nbsp; &nbsp; ... &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp; ]        `}`

* *PatternName*: Name des Pattern-Schlüsselworts. Syntax, die nur Schlüsselwort definiert, ist zulässig: zum Erkennen aller Musterverweise mit einem angegebenen Schlüsselwort.
* *ArgName*: Name des Musterarguments. Muster lassen einen oder mehrere Argumentnamen zu.
* *ArgType*: Typ des Musterarguments `string` (derzeit nur zulässig)
* *PathName*: Name des Pfadarguments. Muster lassen Null- oder einen Pfadnamen zu.
* *PathType*: Typ des Pfadarguments `string` (derzeit nur zulässig)
* *ArgValue1*, *ArgValue2*, ... - Werte `string` der Musterargumente (derzeit sind nur Literale zulässig)
* *PathValue* - Wert des Musterpfads (derzeit sind nur `string` Literale zulässig)
* *ausdruck*: Der *Ausdruck* - ein tabellarischer Ausdruck (z. B. `Logs | where Timestamp > ago(1h)`) oder ein Lambdaausdruck, der auf eine Funktion verweist.

## <a name="pattern-invocation"></a>Musteraufruf

Die Syntax für den Musteraufruf ähnelt der Referenzsyntax für die Bereichstabelle:

* *PatternName* `(` *ArgValue1* [`,` *ArgValue2* ...] `).` *PathValue*
* *PatternName* `(` *ArgValue1* [`,` *ArgValue2* ...] `).["` *PathValue*`"]`

## <a name="remarks"></a>Bemerkungen

**Szenario**

Die pattern-Anweisung wurde für Anwendungen der mittleren Ebene entwickelt, die Benutzerabfragen akzeptieren und diese Abfragen dann an Kusto senden. Solche Anwendungen setzen diesen Benutzerabfragen häufig ein logisches Schemamodell voran (ein Satz von [let-Anweisungen](letstatement.md), möglicherweise durch eine [restrict-Anweisung](restrictstatement.md)suffix ).
In einigen Fällen benötigen diese Anwendungen eine Syntax, die sie Benutzern bereitstellen können, um Entitäten zu referenzieren, die nicht im Voraus bekannt sind und in dem logischen Schema definiert sind, das sie erstellen (entweder, weil sie nicht im Voraus bekannt sind, weil die Anzahl potenzieller Entitäten zu groß ist, um im logischen Schema vordefiniert zu werden).

Pattern löst dieses Szenario wie folgt. Die Anwendung der mittleren Ebene sendet die Abfrage an Kusto, wobei alle Muster deklariert, aber nicht definiert sind. Kusto analysiert dann die Abfrage, und wenn ein oder mehrere Musteraufrufe darin vorhanden sind, wird ein Fehler an die Anwendung der mittleren Ebene zurückgegeben, wobei alle diese Aufrufe explizit aufgeführt sind. Die Anwendung der mittleren Ebene kann dann jeden dieser Verweise auflösen und die Abfrage erneut ausführen, wobei sie ihr dieses Mal die vollständig ausgearbeitete Musterdefinition voranstellt.

**Normalisierungen**

Kusto normalisiert das Muster automatisch, so dass z. B. die folgenden Aufrufe desselben Musters sind und ein einzelnes zurückgemeldet wird:

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

Das bedeutet auch, dass man sie nicht zusammen definieren kann, da sie als die gleichen angesehen werden.

**Platzhalter**

Kusto behandelt Platzhalter in einem Muster nicht auf besondere Weise. In der folgenden Abfrage:

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto meldet einen einzelnen fehlenden `app("ApplicationX").["*"]`Musteraufruf: .

## <a name="examples"></a>Beispiele

Abfragen über mehr als einen einzelnen Musteraufruf:

```kusto
declare pattern A
{
    // ...
};
union (A('a1').Text), (A('a2').Text)
```

|App|SomeText|
|---|---|
|App-#1|Dies ist ein freier Text: 1|
|App-#1|Dies ist ein freier Text: 2|
|App-#1|Dies ist ein freier Text: 3|
|App-#1|Dies ist ein freier Text: 4|
|App-#1|Dies ist ein freier Text: 5|
|App-#2|Dies ist ein freier Text: 9|
|App-#2|Dies ist ein freier Text: 8|
|App-#2|Dies ist ein freier Text: 7|
|App-#2|Dies ist ein freier Text: 6|
|App-#2|Dies ist ein freier Text: 5|

```kusto
declare pattern App;
union (App('a1').Text), (App('a2').Text)
```

Semantischer Fehler:

     SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a1').['Text']","App('a2').['Text']"].

```kusto
declare pattern App;
declare pattern App = (applicationId:string)[scope:string]  
{
    ('a1').['Data']    = { range x from 1 to 5 step 1 | project App = "App #1", Data    = x };
    ('a1').['Metrics'] = { range x from 1 to 5 step 1 | project App = "App #1", Metrics = rand() };
    ('a2').['Data']    = { range x from 1 to 5 step 1 | project App = "App #2", Data    = 10 - x };
    ('a3').['Metrics'] = { range x from 1 to 5 step 1 | project App = "App #3", Metrics = rand() };
};
union (App('a2').Metrics), (App('a3').Metrics) 
```

Semantischer Fehler zurückgegeben:

    SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a2').['Metrics']","App('a3').['Metrics']"].

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
