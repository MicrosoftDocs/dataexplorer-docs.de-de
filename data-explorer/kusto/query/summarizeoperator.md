---
title: Operator zusammenfassen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der zusammengefasste Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.openlocfilehash: ed1808f173d0f779c84f9405987d7395de833120
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663217"
---
# <a name="summarize-operator"></a>summarize-Operator

Erzeugt eine Tabelle, die den Inhalt der Eingabetabelle aggregiert.

```kusto
T | summarize count(), avg(price) by fruit, supplier
```

Eine Tabelle, die die Anzahl und den Durchschnittspreis der einzelnen Früchte von jedem Lieferanten zeigt. Es gibt eine Reihe in der Ausgabe für jede einzelne Kombination von Obst und Lieferant. Die Ausgabespalten zeigen die Anzahl, den Durchschnittspreis, Obst und Lieferanten. Alle anderen Eingabespalten werden ignoriert.

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

Eine Tabelle, die zeigt, wie viele Elemente in jedem Intervall [0, 10,0][10,0, 20,0] usw. Preise aufweisen. In diesem Beispiel ist eine Spalte für die Anzahl und eine für den Preisbereich vorhanden. Alle anderen Eingabespalten werden ignoriert.

**Syntax**

*T* `| summarize` [[*Spalte* `=``,` ] *Aggregation* [ ...]] [`by` [*Spalte* `=`]`,` *GroupExpression* [ ...]]

**Argumente**

* *Column:* Optionaler Name für eine Ergebnisspalte. Nimmt standardmäßig den vom Ausdruck abgeleiteten Namen an.
* *Aggregation:* Ein Aufruf einer [Aggregationsfunktion](summarizeoperator.md#list-of-aggregation-functions) wie `count()` oder `avg()`mit Spaltennamen als Argumenten. Siehe [Liste der Aggregationsfunktionen](summarizeoperator.md#list-of-aggregation-functions).
* *GroupExpression:* Ein Ausdruck für die Spalten, der einen Satz von unterschiedlichen Werten bereitstellt. Normalerweise handelt es sich entweder um einen Spaltennamen, der bereits einen eingeschränkten Satz von Werten bereitstellt, oder um `bin()` mit einer numerischen Spalte oder Zeitspalte als Argument. 

> [!NOTE]
> Wenn die Eingabetabelle leer ist, hängt die Ausgabe davon ab, ob *GroupExpression* verwendet wird:
>
> * Wenn *GroupExpression* nicht bereitgestellt wird, handelt es sich bei der Ausgabe um eine einzelne (leere) Zeile.
> * Wenn *GroupExpression* bereitgestellt wird, enthält die Ausgabe keine Zeilen.

**Rückgabe**

Die Eingabezeilen sind in Gruppen mit denselben Werten der `by` -Ausdrücke angeordnet. Anschließend werden die angegebenen Aggregationsfunktionen über jede Gruppe berechnet, dabei wird eine Zeile für jede Gruppe erzeugt. Das Ergebnis enthält die `by` -Spalten und auch mindestens eine Spalte für jedes berechnete Aggregat. (Einige Aggregationsfunktionen geben mehrere Spalten zurück.)

Das Ergebnis hat so viele Zeilen, `by` wie es verschiedene Kombinationen von Werten gibt (die Null sein können). Wenn keine Gruppenschlüssel bereitgestellt werden, enthält das Ergebnis einen einzelnen Datensatz.

Um über Bereiche numerischer `bin()` Werte zusammenzufassen, verwenden Sie diese, um Bereiche auf diskrete Werte zu reduzieren.

> [!NOTE]
> * Auch wenn Sie beliebige Ausdrücke für die Aggregation und Gruppierung von Ausdrücken bereitstellen können, ist es effizienter, einfache Spaltennamen zu verwenden oder `bin()` auf eine numerische Spalte anzuwenden.
> * Die automatischen stündlichen Lagerplätze für datetime-Spalten werden nicht mehr unterstützt. Verwenden Sie stattdessen explizites Binning. Beispielsweise `summarize by bin(timestamp, 1h)`.

## <a name="list-of-aggregation-functions"></a>Liste der Aggregationsfunktionen

|Funktion|BESCHREIBUNG|
|--------|-----------|
|[any()](any-aggfunction.md)|Gibt einen zufälligen nicht leeren Wert für die Gruppe zurück.|
|[anyif()](anyif-aggfunction.md)|Gibt einen zufälligen nicht leeren Wert für die Gruppe zurück (mit Prädikat)|
|[arg_max()](arg-max-aggfunction.md)|Gibt einen oder mehrere Ausdrücke zurück, wenn das Argument maximiert wird|
|[arg_min()](arg-min-aggfunction.md)|Gibt einen oder mehrere Ausdrücke zurück, wenn das Argument minimiert wird|
|[avg()](avg-aggfunction.md)|Gibt einen Durchschnittswert für die gruppeübergreifende|
|[avgif()](avgif-aggfunction.md)|Gibt einen Durchschnittswert in der gesamten Gruppe zurück (mit Prädikat)|
|[binary_all_and](binary-all-and-aggfunction.md)|Gibt den aggregierten `AND` Wert mithilfe der Binärdatei der Gruppe zurück|
|[binary_all_or](binary-all-or-aggfunction.md)|Gibt den aggregierten `OR` Wert mithilfe der Binärdatei der Gruppe zurück|
|[binary_all_xor](binary-all-xor-aggfunction.md)|Gibt den aggregierten `XOR` Wert mithilfe der Binärdatei der Gruppe zurück|
|[buildschema()](buildschema-aggfunction.md)|Gibt das minimale Schema zurück, `dynamic` das alle Werte der Eingabe zugibt|
|[count()](count-aggfunction.md)|Gibt eine Anzahl der Gruppe zurück|
|[countif()](countif-aggfunction.md)|Gibt eine Zählung mit dem Prädikat der Gruppe zurück|
|[dcount()](dcount-aggfunction.md)|Gibt eine ungefähre eindeutige Anzahl der Gruppenelemente zurück.|
|[dcountif()](dcountif-aggfunction.md)|Gibt eine ungefähre eindeutige Anzahl der Gruppenelemente zurück (mit Prädikat)|
|[make_bag()](make-bag-aggfunction.md)|Gibt einen Eigenschaftensack mit dynamischen Werten innerhalb der Gruppe zurück.|
|[make_bag_if()](make-bag-if-aggfunction.md)|Gibt einen Eigenschaftensack mit dynamischen Werten innerhalb der Gruppe zurück (mit Prädikat)|
|[make_list()](makelist-aggfunction.md)|Gibt eine Liste aller Werte innerhalb der Gruppe zurück|
|[make_list_if()](makelistif-aggfunction.md)|Gibt eine Liste aller Werte innerhalb der Gruppe zurück (mit Prädikat)|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|Gibt eine Liste aller Werte innerhalb der Gruppe zurück, einschließlich NULL-Werten|
|[make_set()](makeset-aggfunction.md)|Gibt einen Satz unterschiedlicher Werte innerhalb der Gruppe zurück|
|[make_set_if()](makesetif-aggfunction.md)|Gibt einen Satz unterschiedlicher Werte innerhalb der Gruppe zurück (mit Prädikat)|
|[max()](max-aggfunction.md)|Gibt den Maximalwert in der Gruppe zurück|
|[maxif()](maxif-aggfunction.md)|Gibt den Maximalwert in der Gruppe zurück (mit Prädikat)|
|[min()](min-aggfunction.md)|Gibt den Mindestwert in der Gruppe zurück|
|[minif()](minif-aggfunction.md)|Gibt den Mindestwert in der Gruppe zurück (mit Prädikat)|
|[percentiles()](percentiles-aggfunction.md)|Gibt die Perzentil-Ungefähre der Gruppe zurück|
|[percentiles_array()](percentiles-aggfunction.md)|Gibt die Perzentilen ungefähr der Gruppe zurück|
|[Perzentilsw()](percentiles-aggfunction.md)|Gibt die gewichtete Perzentilungefähre der Gruppe zurück|
|[percentilesw_array()](percentiles-aggfunction.md)|Gibt die gewichteten Perzentile ungefähr der Gruppe zurück|
|[stdev()](stdev-aggfunction.md)|Gibt die Standardabweichung über die Gruppe hinweg zurück|
|[stdevif()](stdevif-aggfunction.md)|Gibt die Standardabweichung in der Gruppe zurück (mit Prädikat)|
|[sum()](sum-aggfunction.md)|Gibt die Summe der Elemente zurück, die die Gruppe|
|[sumif()](sumif-aggfunction.md)|Gibt die Summe der Elemente zurück, die die Gruppe verwenden (mit Prädikat)|
|[variance()](variance-aggfunction.md)|Gibt die Varianz in der Gruppe zurück|
|[varianceif()](varianceif-aggfunction.md)|Gibt die Varianz in der Gruppe zurück (mit Prädikat)|

## <a name="aggregates-default-values"></a>Aggregiert Standardwerte

Die folgende Tabelle fasst die Standardwerte von Aggregationen zusammen:

Operator       |Standardwert                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    leeres dynamisches Array ([])          
 Alle anderen          |   NULL                           

 Wenn diese Aggregate über Entitäten verwendet werden, die NULL-Werte enthalten, werden die NULL-Werte ignoriert und nehmen nicht an der Berechnung teil (siehe Beispiele unten).

## <a name="examples"></a>Beispiele

![alt text](./Images/aggregations/01.png "01")

**Beispiel**

Bestimmen Sie, welche `ActivityType` `CompletionStatus` eindeutigen Kombinationen in einer Tabelle vorhanden sind. Es gibt keine Aggregationsfunktionen, sondern nur Gruppierte nach Schlüsseln. Die Ausgabe zeigt nur die Spalten für diese Ergebnisse an:

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

**Beispiel**

Sucht den minimalen und maximalen Zeitstempel aller Datensätze in der Tabelle Aktivitäten. Es gibt keine group-by-Klausel, daher gibt es nur eine Zeile in der Ausgabe:

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

**Beispiel**

Erstellen Sie eine Zeile für jeden Kontinent, in der eine Anzahl der Städte angezeigt wird, in denen Aktivitäten stattfinden. Da es nur wenige Werte für "Kontinent" gibt, ist in der "by"-Klausel keine Gruppierungsfunktion erforderlich:

    Activities | summarize cities=dcount(city) by continent

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


**Beispiel**

Im folgenden Beispiel wird ein Histogramm für jeden Aktivitätstyp berechnet. Da `Duration` viele Werte `bin` enthalten sind, können Sie die Werte in 10-Minuten-Intervalle gruppieren:

```kusto
Activities | summarize count() by ActivityType, length=bin(Duration, 10m)
```

|`count_`|`ActivityType`|`length`
|---:|---|---
|`354`| `dancing` | `0:00:00.000`
|`23`|`singing` | `0:00:00.000`
|`2717`|`dancing`|`0:10:00.000`
|`341`|`singing`|`0:10:00.000`
|`725`|`dancing`|`0:20:00.000`
|`2876`|`singing`|`0:20:00.000`
|...

**Beispiel für die Aggregatwerte**

Wenn die `summarize` Eingabe des Operators mindestens einen leeren Gruppenschlüssel hat, ist auch das Ergebnis leer.

Wenn die `summarize` Eingabe des Operators keinen leeren Gruppenschlüssel hat, sind das Ergebnis die Standardwerte der Aggregate, die in : `summarize`

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize any(x), arg_max(x, x), arg_min(x, x), avg(x), buildschema(todynamic(tostring(x))), max(x), min(x), percentile(x, 55), hll(x) ,stdev(x), sum(x), sumif(x, x > 0), tdigest(x), variance(x)
```

|any_x|max_x|max_x_x|min_x|min_x_x|avg_x|schema_x|max_x1|min_x1|percentile_x_55|hll_x|stdev_x|sum_x|sumif_x|tdigest_x|variance_x|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|||||||||||||||||

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize  count(x), countif(x > 0) , dcount(x), dcountif(x, x > 0)
```

|count_x|countif_|dcount_x|dcountif_x|
|---|---|---|---|
|0|0|0|0|

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize  make_set(x), make_list(x)
```

|set_x|list_x|
|---|---|
|[]|[]|

Das Aggregat avg summiert alle Nicht-Nulls und zählt nur diejenigen, die an der Berechnung teilgenommen haben (nicht nulls berücksichtigt).

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

Die reguläre Anzahl zählt nulls: 

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize count(y)
```

|count_y|
|---|
|2|

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize make_set(y), make_set(y)
```

|set_y|set_y1|
|---|---|
|[5.0]|[5.0]|
