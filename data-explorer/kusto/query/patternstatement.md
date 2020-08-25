---
title: 'Pattern-Anweisung: Azure Daten-Explorer'
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
ms.openlocfilehash: 03d183bd042bb75d8bb44f530575bd3b91cb2102
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793804"
---
# <a name="pattern-statement"></a>Pattern-Anweisung

::: zone pivot="azuredataexplorer"

Ein **Muster** ist ein benanntes Ansichts ähnliches Konstrukt, das vordefinierte zeichenfolgentupel zu parameterlosen Funktions Texten zuordnet. Muster sind in zwei Aspekten eindeutig:

* Muster werden mithilfe einer Syntax, die Bereichs bezogenen Tabellen verweisen ähnelt, "aufgerufen".
* Muster verfügen über einen kontrollierten, close-enden Satz von Argument Werten, die zugeordnet werden können, und der Zuordnungsprozess wird von Kusto durchgeführt. Wenn ein Muster deklariert, aber nicht definiert ist, identifiziert Kusto alle Aufrufe für das Muster als Fehler und kennzeichnet es. Diese Identifikation ermöglicht das "auflösen" dieser Muster durch eine Anwendung der mittleren Ebene.

## <a name="pattern-declaration"></a>Muster Deklaration

Die Pattern-Anweisung wird verwendet, um ein Muster zu deklarieren oder zu definieren.
Beispielsweise eine Muster Anweisung, die deklariert, dass `app` ein Muster ist.

```kusto
declare pattern app;
```

Diese Anweisung teilt Kusto `app` mit, dass es sich um ein Muster handelt, aber nicht, wie das Muster aufgelöst werden kann. Daher führt jeder Versuch, dieses Muster in der Abfrage aufzurufen, zu einem bestimmten Fehler und listet alle derartigen Aufrufe auf. Zum Beispiel:

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

Diese Abfrage generiert einen Fehler von Kusto und zeigt an, dass die nächsten musteraufrufe nicht aufgelöst werden können: `app("ApplicationX")["StartEvents"]` und `app("ApplicationX")["StopEvents"]` .

## <a name="syntax"></a>Syntax

`declare``pattern` *PatternName*

## <a name="pattern-definition"></a>Muster Definition

Die Pattern-Anweisung kann auch verwendet werden, um ein Muster zu definieren. In einer Muster Definition werden alle möglichen Aufrufe des Musters explizit angelegt und der entsprechende tabellarische Ausdruck angegeben. Wenn Kusto dann die Abfrage ausführt, wird jeder Muster Aufruf durch den entsprechenden Muster Text ersetzt. Zum Beispiel:

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

## <a name="syntax"></a>Syntax

`declare``pattern` *PatternName*  =  `(` *Argname* `:` *Argtype* [ `,` ...] `)` [ `[` *Pfadname* `:` *pathargtype* `]` ]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* [ `,` *ArgValue2* ...] `)` [ `.[` * pathValue `]` ] `=` `{` *Ausdruck* `};` &nbsp; &nbsp; &nbsp; &nbsp; [ &nbsp; &nbsp; &nbsp; &nbsp; `(` *ArgValue1_2* [ `,` *ArgValue2_2* ...] `)` [ `.[` *PathValue_2* `]` ] `=` `{` *expression_2* `};` &nbsp; &nbsp; &nbsp; &nbsp; ... &nbsp; &nbsp; &nbsp; &nbsp; ]        `}`

* *PatternName*: Name des pattern-Schlüssel Worts. Syntax, die nur das Schlüsselwort definiert, ist zulässig: zum Erkennen aller Muster Verweise mit einem angegebenen Schlüsselwort.
* *Argname*: Name des Muster Arguments. Muster lassen einen oder mehrere Argument Namen zu.
* *Argtype*: Typ des Muster Arguments (derzeit ist nur `string` zulässig)
* *Pfadname*: Name des Path-Arguments. Muster lassen NULL oder einen Pfadnamen zu.
* *PathType*: Typ des Path-Arguments (derzeit `string` ist nur zulässig)
* *ArgValue1*, *ArgValue2*,...-Werte der Muster Argumente (zurzeit `string` sind nur Literale zulässig)
* *PathValue* -Wert des musterpfads (zurzeit `string` sind nur Literale zulässig)
* *Ausdruck*: der *Ausdruck* -ein Tabellen Ausdruck (z. b. `Logs | where Timestamp > ago(1h)` ) oder ein Lambda-Ausdruck, der auf eine Funktion verweist.

## <a name="pattern-invocation"></a>Muster Aufruf

Die Muster Aufruf Syntax ähnelt der Syntax für den Tabellen Verweis im Gültigkeitsbereich.

* *PatternName* `(` *ArgValue1* [ `,` *ArgValue2* ...] `).` *Pfadwert*
* *PatternName* `(` *ArgValue1* [ `,` *ArgValue2* ...] `).["` *Pfadwert*`"]`

## <a name="notes"></a>Notizen

**Szenario**

Die Pattern-Anweisung ist für Anwendungen der mittleren Ebene konzipiert, die Benutzer Abfragen akzeptieren und diese Abfragen dann an Kusto senden. Solche Anwendungen stellen häufig einen Präfix für die Benutzer Abfragen mit einem logischen Schema Modell dar. Das Modell ist ein Satz von [Let-Anweisungen](letstatement.md), der möglicherweise durch eine [Einschränkungs Anweisung](restrictstatement.md)als Suffix versehen wird.

Einige Anwendungen benötigen eine Syntax, die Sie Benutzern bereitstellen können. Die Syntax wird verwendet, um auf Entitäten zu verweisen, die im logischen Schema definiert sind, das von den Anwendungen erstellt wird. Manchmal sind Entitäten jedoch nicht im Voraus bekannt, oder die Anzahl potenzieller Entitäten ist zu groß, um im logischen Schema vordefiniert zu werden.

Das Muster löst dieses Szenario auf folgende Weise. Die Anwendung der mittleren Ebene sendet die Abfrage mit allen deklarierten, aber nicht definierten Mustern an Kusto. Die Abfrage wird dann von Kusto analysiert. Wenn mindestens ein Muster Aufruf vorhanden ist, gibt Kusto einen Fehler an die Anwendung der mittleren Ebene zurück, bei der alle diese Aufrufe explizit aufgelistet sind. Die Anwendung der mittleren Ebene kann dann jeden dieser Verweise auflösen und die Abfrage erneut ausführen. Diese Zeit wird der vollständig ausgearbeiteten Muster Definition vorangestellt.

**Normalisierungen**

Kusto normalisiert das Muster automatisch. Im folgenden finden Sie beispielsweise alle Aufrufe desselben Musters, und eine einzelne wird zurückgemeldet.

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

Dies bedeutet auch, dass Sie Sie nicht mehr definieren können, da Sie als identisch angesehen werden.

**Platzhalter**

Die Platzhalter in einem Muster werden von Kusto auf keine besondere Weise behandelt. Beispielsweise in der folgenden Abfrage.

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto meldet einen einzelnen fehlenden Muster Aufruf: `app("ApplicationX").["*"]` .

## <a name="examples"></a>Beispiele

Abfragen über mehr als einen einzelnen Muster Aufruf.

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

**Semantik Fehler**:

> SEM0036: mindestens ein Muster Verweis wurde nicht deklariert. Erkannte Muster Verweise: ["app (' a1 '). [' Text "]", "app (' a2 '). [' Text '] "].

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

**Semantischer Fehler zurückgegeben**:

> SEM0036: mindestens ein Muster Verweis wurde nicht deklariert. Erkannte Muster Verweise: ["app (' a2 '). [' Metriken "]", "app (' a3 '). [' Metriken '] "].

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
