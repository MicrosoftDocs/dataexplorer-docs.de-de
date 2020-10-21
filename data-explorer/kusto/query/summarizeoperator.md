---
title: 'Zusammenfassungs Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den Zusammenfassungs Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.openlocfilehash: 1317f9767182ad76f226fae6995763ea832cd36a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250857"
---
# <a name="summarize-operator"></a>summarize-Operator

Erzeugt eine Tabelle, die den Inhalt der Eingabetabelle aggregiert.

```kusto
Sales | summarize NumTransactions=count(), Total=sum(UnitPrice * NumUnits) by Fruit, StartOfMonth=startofmonth(SellDateTime)
```

Gibt eine Tabelle zurück, in der die Anzahl der verkauften Transaktionen und der Gesamtbetrag pro Frucht und Monat verkauft werden.
In den Ausgabespalten wird die Anzahl von Transaktionen, der Transaktionswert, der Frucht und der DateTime-Wert für den Anfang des Monats, in dem die Transaktion aufgezeichnet wurde, angezeigt.

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

Eine Tabelle, die zeigt, wie viele Elemente in jedem Intervall [0, 10,0][10,0, 20,0] usw. Preise aufweisen. In diesem Beispiel ist eine Spalte für die Anzahl und eine für den Preisbereich vorhanden. Alle anderen Eingabespalten werden ignoriert.

## <a name="syntax"></a>Syntax

*T* `| summarize` [[*Column* `=` ] *Aggregation* [ `,` ...]] [ `by` [*Column* `=` ] *groupexpression* [ `,` ...]]

## <a name="arguments"></a>Argumente

* *Column:* Optionaler Name für eine Ergebnisspalte. Nimmt standardmäßig den vom Ausdruck abgeleiteten Namen an.
* *Aggregation:* Ein Aufrufe einer [Aggregations Funktion](summarizeoperator.md#list-of-aggregation-functions) , z `count()` `avg()` . b. oder, mit Spaltennamen als Argumente. Weitere Informationen finden Sie [in der Liste der Aggregations Funktionen](summarizeoperator.md#list-of-aggregation-functions).
* *Groupexpression:* Ein Skalarausdruck, der auf die Eingabedaten verweisen kann.
  Die Ausgabe enthält so viele Datensätze, wie es unterschiedliche Werte aller Gruppierungs Ausdrücke gibt.

> [!NOTE]
> Wenn die Eingabe Tabelle leer ist, ist die Ausgabe davon abhängig, ob *groupexpression* verwendet wird:
>
> * Wenn *groupexpression* nicht angegeben wird, ist die Ausgabe eine einzelne (leere) Zeile.
> * Wenn *groupexpression* bereitgestellt wird, weist die Ausgabe keine Zeilen auf.

## <a name="returns"></a>Rückgabe

Die Eingabezeilen sind in Gruppen mit denselben Werten der `by` -Ausdrücke angeordnet. Anschließend werden die angegebenen Aggregationsfunktionen über jede Gruppe berechnet, dabei wird eine Zeile für jede Gruppe erzeugt. Das Ergebnis enthält die `by` -Spalten und auch mindestens eine Spalte für jedes berechnete Aggregat. (Einige Aggregationsfunktionen geben mehrere Spalten zurück.)

Das Ergebnis verfügt über so viele Zeilen, wie es unterschiedliche Kombinationen von `by` Werten gibt (die NULL sein können). Wenn keine Gruppenschlüssel bereitgestellt werden, verfügt das Ergebnis über einen einzelnen Datensatz.

Um Bereiche numerischer Werte zusammenzufassen, verwenden `bin()` Sie, um Bereiche auf diskrete Werte zu reduzieren.

> [!NOTE]
> * Auch wenn Sie beliebige Ausdrücke für die Aggregation und Gruppierung von Ausdrücken bereitstellen können, ist es effizienter, einfache Spaltennamen zu verwenden oder `bin()` auf eine numerische Spalte anzuwenden.
> * Die automatischen stündlichen Behälter für DateTime-Spalten werden nicht mehr unterstützt. Verwenden Sie stattdessen die explizite Klassifizierung. Beispiel: `summarize by bin(timestamp, 1h)`.

## <a name="list-of-aggregation-functions"></a>Liste der Aggregations Funktionen

|Funktion|BESCHREIBUNG|
|--------|-----------|
|[Any ()](any-aggfunction.md)|Gibt einen zufälligen, nicht leeren Wert für die Gruppe zurück.|
|[anyif()](anyif-aggfunction.md)|Gibt einen zufälligen, nicht leeren Wert für die Gruppe zurück (mit Prädikat).|
|[arg_max()](arg-max-aggfunction.md)|Gibt einen oder mehrere Ausdrücke zurück, wenn das Argument maximiert ist.|
|[arg_min()](arg-min-aggfunction.md)|Gibt einen oder mehrere Ausdrücke zurück, wenn das Argument minimiert wird.|
|[AVG ()](avg-aggfunction.md)|Gibt einen durchschnittlichen Wert in der Gruppe zurück.|
|[avgif()](avgif-aggfunction.md)|Gibt einen durchschnittlichen Wert über die Gruppe zurück (mit Prädikat).|
|[binary_all_and](binary-all-and-aggfunction.md)|Gibt den aggregierten Wert mithilfe der Binärdatei der Gruppe zurück. `AND`|
|[binary_all_or](binary-all-or-aggfunction.md)|Gibt den aggregierten Wert mithilfe der Binärdatei der Gruppe zurück. `OR`|
|[binary_all_xor](binary-all-xor-aggfunction.md)|Gibt den aggregierten Wert mithilfe der Binärdatei der Gruppe zurück. `XOR`|
|[buildschema()](buildschema-aggfunction.md)|Gibt das minimale Schema zurück, das alle Werte der `dynamic` Eingabe zulässt.|
|[count ()](count-aggfunction.md)|Gibt die Anzahl der Gruppe zurück.|
|[countif()](countif-aggfunction.md)|Gibt eine Anzahl mit dem Prädikat der Gruppe zurück.|
|[dcount()](dcount-aggfunction.md)|Gibt eine ungefähre unterschiedliche Anzahl der Group-Elemente zurück.|
|[dcountif()](dcountif-aggfunction.md)|Gibt eine ungefähre unterschiedliche Anzahl von Gruppenelementen zurück (mit Prädikat).|
|[make_bag()](make-bag-aggfunction.md)|Gibt einen Eigenschaften Behälter dynamischer Werte in der Gruppe zurück.|
|[make_bag_if()](make-bag-if-aggfunction.md)|Gibt einen Eigenschaften Behälter dynamischer Werte in der Gruppe zurück (mit Prädikat).|
|[make_list()](makelist-aggfunction.md)|Gibt eine Liste aller Werte in der Gruppe zurück.|
|[make_list_if()](makelistif-aggfunction.md)|Gibt eine Liste aller Werte in der Gruppe zurück (mit Prädikat).|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|Gibt eine Liste aller Werte in der Gruppe zurück, einschließlich NULL-Werten.|
|[make_set()](makeset-aggfunction.md)|Gibt einen Satz unterschiedlicher Werte in der Gruppe zurück.|
|[make_set_if()](makesetif-aggfunction.md)|Gibt einen Satz unterschiedlicher Werte in der Gruppe zurück (mit Prädikat).|
|[max()](max-aggfunction.md)|Gibt den Höchstwert in der Gruppe zurück.|
|[maxif()](maxif-aggfunction.md)|Gibt den maximalen Wert für die Gruppe (mit Prädikat) zurück.|
|[min()](min-aggfunction.md)|Gibt den Mindestwert in der Gruppe zurück.|
|[minif()](minif-aggfunction.md)|Gibt den minimalen Wert für die Gruppe (mit Prädikat) zurück.|
|[Perzentile ()](percentiles-aggfunction.md)|Gibt die ungefähre Quantil der Gruppe zurück.|
|[percentiles_array ()](percentiles-aggfunction.md)|Gibt die Quantilen der Gruppe zurück.|
|[percentilesw ()](percentiles-aggfunction.md)|Gibt das gewichtete Perzentil der Gruppe zurück.|
|[percentilesw_array ()](percentiles-aggfunction.md)|Gibt die gewichteten Quantilen der Gruppe zurück.|
|[StDev ()](stdev-aggfunction.md)|Gibt die Standardabweichung in der Gruppe zurück.|
|[stdevif()](stdevif-aggfunction.md)|Gibt die Standardabweichung für die Gruppe (mit Prädikat) zurück.|
|[Sum ()](sum-aggfunction.md)|Gibt die Summe der Elemente in der Gruppe zurück.|
|[sumif()](sumif-aggfunction.md)|Gibt die Summe der Elemente in der Gruppe zurück (mit Prädikat).|
|[variance()](variance-aggfunction.md)|Gibt die Varianz innerhalb der Gruppe zurück.|
|[varianceif()](varianceif-aggfunction.md)|Gibt die Varianz über die Gruppe zurück (mit Prädikat).|

## <a name="aggregates-default-values"></a>Aggregiert Standardwerte

In der folgenden Tabelle werden die Standardwerte von Aggregationen zusammengefasst:

Operator       |Standardwert                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    leeres dynamisches Array ([])          
 Alle anderen          |   NULL                           

 Wenn diese Aggregate für Entitäten verwendet werden, die NULL-Werte enthalten, werden die NULL-Werte ignoriert und nicht an der Berechnung beteiligt (siehe Beispiele unten).

## <a name="examples"></a>Beispiele

:::image type="content" source="images/summarizeoperator/summarize-price-by-supplier.png" alt-text="Preis nach Obst und Lieferant zusammenfassen":::

## <a name="example-unique-combination"></a>Beispiel: eindeutige Kombination

Bestimmen Sie, welche eindeutigen Kombinationen von `ActivityType` und `CompletionStatus` in einer Tabelle vorhanden sind. Es gibt keine Aggregations Funktionen, sondern nur Gruppieren nach Schlüsseln. In der Ausgabe werden nur die Spalten für diese Ergebnisse angezeigt:

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

## <a name="example-minimum-and-maximum-timestamp"></a>Beispiel: minimaler und maximaler Zeitstempel

Sucht den minimalen und maximalen Zeitstempel aller Datensätze in der Aktivitäts Tabelle. Es gibt keine group-by-Klausel, daher gibt es nur eine Zeile in der Ausgabe:

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

## <a name="example-distinct-count"></a>Beispiel: unterschiedliche Anzahl

Erstellen Sie für jeden Kontinent eine Zeile, die die Anzahl der Städte anzeigt, in denen Aktivitäten auftreten. Da es nur wenige Werte für "Kontinent" gibt, ist keine Gruppierungs Funktion in der by-Klausel erforderlich:

```kusto
Activities | summarize cities=dcount(city) by continent
```

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


## <a name="example-histogram"></a>Beispiel: Histogramm

Im folgenden Beispiel wird ein Histogramm für jeden Aktivitätstyp berechnet. Da `Duration` viele Werte aufweist, verwenden `bin` Sie, um die Werte in 10-Minuten-Intervallen zu gruppieren:

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

Wenn die Eingabe des `summarize` Operators mindestens einen leeren Group-by-Schlüssel aufweist, ist das Ergebnis ebenfalls leer.

Wenn die Eingabe des `summarize` Operators keinen leeren Group-by-Schlüssel hat, sind das Ergebnis die Standardwerte der Aggregate, die in verwendet werden `summarize` :

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

Das aggregierte Durchschnittl addiert alle nicht-NULL-Werte und zählt nur die Werte, die an der Berechnung beteiligt waren (keine NULL-Werte berücksichtigen).

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

Die reguläre Anzahl zählt Nullen: 

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
|[5,0]|[5,0]|
