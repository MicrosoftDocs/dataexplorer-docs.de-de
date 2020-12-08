---
title: 'summarize-Operator: Azure Data Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der summarize-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.localizationpriority: high
ms.openlocfilehash: 810eba264c717d156f74b9958edecb712d58a4fd
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513198"
---
# <a name="summarize-operator"></a>summarize-Operator

Erzeugt eine Tabelle, die den Inhalt der Eingabetabelle aggregiert.

```kusto
Sales | summarize NumTransactions=count(), Total=sum(UnitPrice * NumUnits) by Fruit, StartOfMonth=startofmonth(SellDateTime)
```

Gibt eine Tabelle zurück, in der die Anzahl der Verkaufstransaktionen und der Gesamtbetrag pro Frucht und Verkaufsmonat aufgeführt werden.
In den Ausgabespalten wird die Anzahl von Transaktionen, der Transaktionswert, die Frucht und der DateTime-Wert für den Anfang des Monats angezeigt, in dem die Transaktion aufgezeichnet wurde.

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

Eine Tabelle, die zeigt, wie viele Elemente in jedem Intervall [0, 10,0][10,0, 20,0] usw. Preise aufweisen. In diesem Beispiel ist eine Spalte für die Anzahl und eine für den Preisbereich vorhanden. Alle anderen Eingabespalten werden ignoriert.

## <a name="syntax"></a>Syntax

*T* `| summarize` [[*Column* `=`] *Aggregation* [`,` ...]] [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

## <a name="arguments"></a>Argumente

* *Column:* Optionaler Name für eine Ergebnisspalte. Nimmt standardmäßig den vom Ausdruck abgeleiteten Namen an.
* *Aggregation:* Ein Aufruf einer [Aggregationsfunktion](summarizeoperator.md#list-of-aggregation-functions), z. B. `count()` oder `avg()`, mit Spaltennamen als Argumenten. Weitere Informationen finden Sie in der [Liste der Aggregationsfunktionen](summarizeoperator.md#list-of-aggregation-functions).
* *GroupExpression:* Ein Skalarausdruck, der auf die Eingabedaten verweisen kann.
  Die Ausgabe enthält so viele Datensätze, wie es unterschiedliche Werte aller Gruppierungsausdrücke gibt.

> [!NOTE]
> Wenn die Eingabetabelle leer ist, ist die Ausgabe davon abhängig, ob *GroupExpression* verwendet wird:
>
> * Wenn *GroupExpression* nicht angegeben wird, ist die Ausgabe eine einzelne (leere) Zeile.
> * Wenn *GroupExpression* angegeben wird, weist die Ausgabe keine Zeilen auf.

## <a name="returns"></a>Gibt zurück

Die Eingabezeilen sind in Gruppen mit denselben Werten der `by` -Ausdrücke angeordnet. Anschließend werden die angegebenen Aggregationsfunktionen über jede Gruppe berechnet, dabei wird eine Zeile für jede Gruppe erzeugt. Das Ergebnis enthält die `by` -Spalten und auch mindestens eine Spalte für jedes berechnete Aggregat. (Einige Aggregationsfunktionen geben mehrere Spalten zurück.)

Das Ergebnis umfasst genauso viele Zeilen, wie unterschiedliche Kombinationen von `by`-Werten vorhanden sind (kann null sein). Wenn keine Gruppenschlüssel bereitgestellt werden, verfügt das Ergebnis über einen einzelnen Datensatz.

Um Zusammenfassungen über Bereiche von numerischen Werten hinweg zu erstellen, verwenden Sie `bin()`, um Bereiche auf diskrete Werte zu reduzieren.

> [!NOTE]
> * Auch wenn Sie beliebige Ausdrücke für die Aggregation und Gruppierung von Ausdrücken bereitstellen können, ist es effizienter, einfache Spaltennamen zu verwenden oder `bin()` auf eine numerische Spalte anzuwenden.
> * Die automatischen stündlichen Bins für DateTime-Spalten werden nicht mehr unterstützt. Verwenden Sie stattdessen explizite Quantisierung. Beispiel: `summarize by bin(timestamp, 1h)`.

## <a name="list-of-aggregation-functions"></a>Liste der Aggregationsfunktionen

|Funktion|BESCHREIBUNG|
|--------|-----------|
|[any()](any-aggfunction.md)|Gibt einen zufälligen, nicht leeren Wert für die Gruppe zurück.|
|[anyif()](anyif-aggfunction.md)|Gibt einen zufälligen, nicht leeren Wert für die Gruppe (mit Prädikat) zurück.|
|[arg_max()](arg-max-aggfunction.md)|Gibt mindestens einen Ausdruck zurück, wenn das Argument maximiert ist.|
|[arg_min()](arg-min-aggfunction.md)|Gibt mindestens einen Ausdruck zurück, wenn das Argument minimiert ist.|
|[avg()](avg-aggfunction.md)|Gibt einen Durchschnittswert für die Gruppe zurück.|
|[avgif()](avgif-aggfunction.md)|Gibt einen Durchschnittswert für die Gruppe (mit Prädikat) zurück.|
|[binary_all_and](binary-all-and-aggfunction.md)|Gibt einen aggregierten Wert mit dem binären `AND` der Gruppe zurück.|
|[binary_all_or](binary-all-or-aggfunction.md)|Gibt einen aggregierten Wert mit dem binären `OR` der Gruppe zurück.|
|[binary_all_xor](binary-all-xor-aggfunction.md)|Gibt einen aggregierten Wert mit dem binären `XOR` der Gruppe zurück.|
|[buildschema()](buildschema-aggfunction.md)|Gibt das minimale Schema zurück, das alle Werte der `dynamic`-Eingabe zulässt.|
|[count()](count-aggfunction.md)|Gibt eine Anzahl der Gruppe zurück.|
|[countif()](countif-aggfunction.md)|Gibt eine Anzahl mit dem Prädikat der Gruppe zurück.|
|[dcount()](dcount-aggfunction.md)|Gibt eine ungefähre eindeutige Anzahl der Gruppenelemente zurück.|
|[dcountif()](dcountif-aggfunction.md)|Gibt eine ungefähre eindeutige Anzahl der Gruppenelemente (mit Prädikat) zurück.|
|[make_bag()](make-bag-aggfunction.md)|Gibt einen Eigenschaftenbehälter dynamischer Werte in der Gruppe zurück.|
|[make_bag_if()](make-bag-if-aggfunction.md)|Gibt einen Eigenschaftenbehälter dynamischer Werte in der Gruppe (mit Prädikat) zurück.|
|[make_list()](makelist-aggfunction.md)|Gibt eine Liste aller Werte in der Gruppe zurück.|
|[make_list_if()](makelistif-aggfunction.md)|Gibt eine Liste aller Werte in der Gruppe (mit Prädikat) zurück.|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|Gibt eine Liste aller Werte in der Gruppe einschließlich der NULL-Werte zurück.|
|[make_set()](makeset-aggfunction.md)|Gibt einen Satz unterschiedlicher Werte in der Gruppe zurück.|
|[make_set_if()](makesetif-aggfunction.md)|Gibt einen Satz unterschiedlicher Werte in der Gruppe (mit Prädikat) zurück.|
|[max()](max-aggfunction.md)|Gibt den Höchstwert in der Gruppe zurück.|
|[maxif()](maxif-aggfunction.md)|Gibt einen Maximalwert für die Gruppe (mit Prädikat) zurück.|
|[min()](min-aggfunction.md)|Gibt den Mindestwert in der Gruppe zurück.|
|[minif()](minif-aggfunction.md)|Gibt den Mindestwert für die Gruppe (mit Prädikat) zurück.|
|[percentiles()](percentiles-aggfunction.md)|Gibt das ungefähre Quantil der Gruppe zurück.|
|[percentiles_array()](percentiles-aggfunction.md)|Gibt das ungefähre Quantil der Gruppe zurück.|
|[percentilesw()](percentiles-aggfunction.md)|Gibt das gewichtete ungefähre Quantil der Gruppe zurück.|
|[percentilesw_array()](percentiles-aggfunction.md)|Gibt das gewichtete ungefähre Quantil der Gruppe zurück.|
|[stdev()](stdev-aggfunction.md)|Gibt die Standardabweichung für die Gruppe zurück.|
|[stdevif()](stdevif-aggfunction.md)|Gibt die Standardabweichung für die Gruppe (mit Prädikat) zurück.|
|[sum()](sum-aggfunction.md)|Gibt die Summe der Elemente in der Gruppe zurück.|
|[sumif()](sumif-aggfunction.md)|Gibt die Summe der Elemente in der Gruppe (mit Prädikat) zurück.|
|[variance()](variance-aggfunction.md)|Gibt die Varianz für die Gruppe zurück.|
|[varianceif()](varianceif-aggfunction.md)|Gibt die Varianz für die Gruppe (mit Prädikat) zurück.|

## <a name="aggregates-default-values"></a>Standardwerte von Aggregationen

In der folgenden Tabelle werden die Standardwerte von Aggregationen zusammengefasst.

Operator       |Standardwert                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    Leeres dynamisches Array              ([])          
 Alle anderen          |   NULL                           

 Wenn diese Aggregate für Entitäten verwendet werden, die NULL-Werte enthalten, werden die NULL-Werte ignoriert und nicht in die Berechnung einbezogen (siehe Beispiele unten).

## <a name="examples"></a>Beispiele

:::image type="content" source="images/summarizeoperator/summarize-price-by-supplier.png" alt-text="Zusammenfassen der Preise nach Obst und Lieferant":::

## <a name="example-unique-combination"></a>Beispiel: Eindeutige Kombination

Bestimmen Sie, welche eindeutigen Kombinationen aus `ActivityType` und `CompletionStatus` in einer Tabelle vorhanden sind. Es gibt keine Aggregationsfunktionen, sondern nur group-by-Schlüssel. In der Ausgabe werden nur die Spalten für diese Ergebnisse angezeigt:

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

## <a name="example-minimum-and-maximum-timestamp"></a>Beispiel: Minimaler und maximaler Zeitstempel

Ermittelt die minimalen und maximalen Zeitstempel aller Datensätze in der Tabelle „Activities“. Es gibt keine group-by-Klausel, daher gibt es nur eine Zeile in der Ausgabe:

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

## <a name="example-distinct-count"></a>Beispiel: Eindeutige Anzahl

Erstellt für jeden Kontinent eine Zeile, die die Anzahl der Städte anzeigt, in denen Aktivitäten auftreten. Da es nur wenige Werte für „continent“ gibt, ist keine Gruppierungsfunktion in der by-Klausel erforderlich:

```kusto
Activities | summarize cities=dcount(city) by continent
```

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


## <a name="example-histogram"></a>Beispiel: Histogramm

Im folgenden Beispiel wird ein Histogramm für jeden Aktivitätstyp berechnet. Da `Duration` viele Werte aufweist, verwenden Sie `bin`, um die Werte in 10-Minuten-Intervallen zu gruppieren:

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

**Beispiel für die Standardwerte von Aggregaten**

Wenn die Eingabe des `summarize`-Operators mindestens einen leeren group-by-Schlüssel aufweist, ist das Ergebnis ebenfalls leer.

Wenn die Eingabe des `summarize`-Operators keinen leeren group-by-Schlüssel aufweist, enthält das Ergebnis die Standardwerte der Aggregate, die in `summarize` verwendet werden:

```kusto
datatable(x:long)[]
| summarize any(x), arg_max(x, x), arg_min(x, x), avg(x), buildschema(todynamic(tostring(x))), max(x), min(x), percentile(x, 55), hll(x) ,stdev(x), sum(x), sumif(x, x > 0), tdigest(x), variance(x)
```

|any_x|max_x|max_x_x|min_x|min_x_x|avg_x|schema_x|max_x1|min_x1|percentile_x_55|hll_x|stdev_x|sum_x|sumif_x|tdigest_x|variance_x|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|||||||||||||||||

```kusto
datatable(x:long)[]
| summarize  count(x), countif(x > 0) , dcount(x), dcountif(x, x > 0)
```

|count_x|countif_|dcount_x|dcountif_x|
|---|---|---|---|
|0|0|0|0|

```kusto
datatable(x:long)[]
| summarize  make_set(x), make_list(x)
```

|set_x|list_x|
|---|---|
|[]|[]|

Das Aggregat avg addiert alle Nicht-NULL-Werte und zählt nur die Werte, die an der Berechnung beteiligt waren (es werden keine NULL-Werte berücksichtigt).

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

Für die reguläre Anzahl werden NULL-Werte mitgezählt: 

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
