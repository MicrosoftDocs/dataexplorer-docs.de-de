---
title: 'Pattern-Anweisung: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Muster Anweisung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 97fc8361feb3d8dddb7d0722ce741ef44bd4cec6
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737265"
---
# <a name="pattern-statement"></a>Pattern-Anweisung

::: zone pivot="azuredataexplorer"

Ein **Muster** ist ein benanntes Ansichts ähnliches Konstrukt, das vordefinierte zeichenfolgentupel zu parameterlosen Funktions Texten zuordnet. Muster sind in zwei Aspekten eindeutig:

* Muster werden mithilfe einer Syntax, die Bereichs bezogenen Tabellen verweisen ähnelt, "aufgerufen".
* Muster verfügen über einen kontrollierten, close-enden Satz von Argument Werten, die zugeordnet werden können, und der Zuordnungsprozess wird von Kusto durchgeführt. Dies bedeutet, dass, wenn ein Muster deklariert, aber nicht definiert ist, Kusto als Fehler alle Aufrufe des Musters identifiziert und gekennzeichnet wird, sodass diese Muster durch eine Anwendung der mittleren Ebene "aufgelöst" werden können.


## <a name="pattern-declaration"></a>Muster Deklaration
Die Pattern-Anweisung wird verwendet, um ein Muster zu deklarieren oder zu definieren.
Beispielsweise ist im folgenden eine Pattern-Anweisung angegeben, `app` die als Muster deklariert:

```kusto
declare pattern app;
```

Diese Anweisung weist Kusto an `app` , dass ein Muster ist, aber nicht, wie das Muster aufgelöst werden kann. Daher führt jeder Versuch, dieses Muster in der Abfrage aufzurufen, zu einem bestimmten Fehler, der alle derartigen Aufrufe auflistet. Beispiel:

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

Diese Abfrage generiert einen Fehler von Kusto und zeigt an, dass die folgenden musteraufrufe nicht aufgelöst werden können `app("ApplicationX")["StartEvents"]` : `app("ApplicationX")["StopEvents"]`und.

**Syntax**

`declare``pattern` *PatternName*

## <a name="pattern-definition"></a>Muster Definition

Die Pattern-Anweisung kann auch verwendet werden, um ein Muster zu definieren. In einer Muster Definition werden alle möglichen Aufrufe des Musters explizit angelegt und der entsprechende tabellarische Ausdruck angegeben. Wenn Kusto dann die Abfrage ausführt, wird jeder Muster Aufruf durch den entsprechenden Muster Text ersetzt. Beispiel:

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

Der Ausdruck, der für jedes übereinstimmende Muster bereitgestellt wird, ist entweder ein Tabellenname oder ein Verweis auf eine [Let-Anweisung](letstatement.md).

**Syntax**

`declare``pattern` *PatternName* = `(`*argname* `:` *argtype* [`,` ...] `)` [`[` *Pfadname* `:` *pathargtype* `]`]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* [`,` *ArgValue2* ...] `)` [ `.[` * PathValue `]` ] `=` `{` *Ausdruck* `};` &nbsp; `,` *ArgValue2_2* *ArgValue1_2* [ &nbsp; ArgValue1_2 &nbsp; ArgValue2_2...] &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; `(` `)` [ `.[` *PathValue_2* `{` *expression_2* ] `=` expression_2`};` .. &nbsp; . `]` &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp; ]        `}`

* *PatternName*: Name des pattern-Schlüssel Worts. Syntax, die nur das Schlüsselwort definiert, ist zulässig: zum Erkennen aller Muster Verweise mit einem angegebenen Schlüsselwort.
* *Argname*: Name des Muster Arguments. Muster lassen einen oder mehrere Argument Namen zu.
* *Argtype*: Typ des Muster Arguments (derzeit ist nur `string` zulässig)
* *Pfadname*: Name des Path-Arguments. Muster lassen NULL oder einen Pfadnamen zu.
* *PathType*: Typ des Path-Arguments (derzeit ist `string` nur zulässig)
* *ArgValue1*, *ArgValue2*,...-Werte der Muster Argumente (zurzeit sind `string` nur Literale zulässig)
* *PathValue* -Wert des musterpfads (zurzeit `string` sind nur Literale zulässig)
* *Ausdruck*: der *Ausdruck* -ein Tabellen Ausdruck (z. b `Logs | where Timestamp > ago(1h)`.) oder ein Lambda-Ausdruck, der auf eine Funktion verweist.

## <a name="pattern-invocation"></a>Muster Aufruf

Die Muster Aufruf Syntax ähnelt der Syntax für den Tabellen Verweis im Gültigkeitsbereich:

* *PatternName* `(` *ArgValue1* [`,` *ArgValue2* ...] `).` *Pfadwert*
* *PatternName* `(` *ArgValue1* [`,` *ArgValue2* ...] `).["` *Pfadwert*`"]`

## <a name="remarks"></a>Bemerkungen

**Szenario**

Die Pattern-Anweisung ist für Anwendungen der mittleren Ebene konzipiert, die Benutzer Abfragen akzeptieren und diese Abfragen dann an Kusto senden. Solche Anwendungen stellen häufig einem logischen Schema Modell (einem Satz von [Let-Anweisungen](letstatement.md), möglicherweise durch eine [Einschränkungs Anweisung](restrictstatement.md)) ein Präfix für diese Benutzer Abfragen vor.
In einigen Fällen benötigen diese Anwendungen eine Syntax, mit der Benutzer auf Entitäten verweisen können, die nicht im Voraus bekannt sein können und im logischen Schema definiert sind, das Sie konstruieren (weil Sie nicht im Voraus bekannt sind, weil die Anzahl potenzieller Entitäten zu groß ist, um im logischen Schema vordefiniert zu werden.

Muster lösen Sie dieses Szenario auf folgende Weise. Die Anwendung der mittleren Ebene sendet die Abfrage mit allen deklarierten, aber nicht definierten Mustern an Kusto. Kusto analysiert dann die Abfrage, und wenn ein oder mehrere musteraufrufe in vorhanden sind, wird ein Fehler an die Anwendung der mittleren Ebene zurückgegeben, und alle diese Aufrufe werden explizit aufgelistet. Die Anwendung der mittleren Ebene kann dann jeden dieser Verweise auflösen und die Abfrage erneut ausführen. dieses Mal wird ihr die vollständig ausgearbeitete Muster Definition vorangestellt.

**Normalisierungen**

Kusto normalisiert das Muster automatisch, sodass z. b. im folgenden alle Aufrufe desselben Musters aufgeführt werden, und eine einzelne Meldung wird zurückgegeben:

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

Dies bedeutet auch, dass Sie nicht in einer Weise definiert werden können, da Sie als identisch angesehen werden.

**Platzhalter**

Die Platzhalter in einem Muster werden von Kusto auf keine besondere Weise behandelt. Beispielsweise in der folgenden Abfrage:

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto meldet einen einzelnen fehlenden Muster Aufruf: `app("ApplicationX").["*"]`.

## <a name="examples"></a>Beispiele

Abfragen über mehrere musteraufrufe:

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
|App-#1|Dies ist ein kostenloser Text: 3|
|App-#1|Dies ist ein kostenloser Text: 4|
|App-#1|Dies ist ein kostenloser Text: 5|
|App-#2|Dies ist ein kostenloser Text: 9|
|App-#2|Dies ist ein kostenloser Text: 8|
|App-#2|Dies ist ein kostenloser Text: 7|
|App-#2|Dies ist ein kostenloser Text: 6|
|App-#2|Dies ist ein kostenloser Text: 5|

```kusto
declare pattern App;
union (App('a1').Text), (App('a2').Text)
```

Semantik Fehler:

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

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
