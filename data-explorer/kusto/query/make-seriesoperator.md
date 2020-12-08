---
title: 'make-series-Operator: Azure Data Explorer'
description: In diesem Artikel wird der make-series-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.localizationpriority: high
ms.openlocfilehash: 6357afeb0a5673584e27b84a231e3c65f897b8fc
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512365"
---
# <a name="make-series-operator"></a>make-series-Operator

Erstellt eine Reihe angegebener aggregierter Werte entlang einer angegebenen Achse.

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

## <a name="syntax"></a>Syntax

*T* `| make-series` [*MakeSeriesParamters*] [*Column* `=`] *Aggregation* [`default` `=` *DefaultValue*] [`,` ...] `on` *AxisColumn* [`from` *start*] [`to` *end*] `step` *step* [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

## <a name="arguments"></a>Argumente

* *Column:* Optionaler Name für eine Ergebnisspalte. Nimmt standardmäßig den vom Ausdruck abgeleiteten Namen an.
* *DefaultValue:* Standardwert, der anstelle von fehlenden Werten verwendet wird. Ist keine Zeile mit bestimmten *AxisColumn*- und *GroupExpression*-Werten vorhanden, wird das entsprechende Element in den Ergebnissen mit einem *Standardwert* zugewiesen. Wenn *DefaultValue* ausgelassen wird, wird 0 angenommen. 
* *Aggregation:* Ein Aufruf einer [Aggregationsfunktion](make-seriesoperator.md#list-of-aggregation-functions), z. B. `count()` oder `avg()`, mit Spaltennamen als Argumenten. Weitere Informationen finden Sie in der [Liste der Aggregationsfunktionen](make-seriesoperator.md#list-of-aggregation-functions). Nur Aggregationsfunktionen, die numerische Ergebnisse zurückgeben, können mit dem `make-series`-Operator verwendet werden.
* *AxisColumn:* Eine Spalte, nach der die Reihe sortiert wird. Sie kann als Zeitachse verwendet werden, es werden jedoch neben `datetime` beliebige numerische Typen akzeptiert.
* *start*: (optional) Der niedrige gebundene Wert von *AxisColumn* für jede der zu erstellenden Reihen. *start*, *end* und *step* werden verwendet, um ein Array mit *AxisColumn*-Werten innerhalb eines bestimmten Bereichs und mithilfe des angegebenen *step*-Werts zu erstellen. Alle *Aggregation*-Werte werden diesem Array entsprechend sortiert. Dieses *AxisColumn*-Array ist auch die letzte Ausgabespalte in der Ausgabe, die denselben Namen wie *AxisColumn* aufweist. Wenn kein *start*-Wert angegeben wird, ist der Start der erste Bin (step), der Daten in jeder Reihe aufweist.
* *end*: (optional) Der hoch gebundene (nicht inklusive) Wert der *AxisColumn*. Der letzte Index der Zeitreihe ist kleiner als dieser Wert (und setzt sich aus *start* zuzüglich eines ganzzahligen Vielfachen von *step* zusammen – dieser Wert ist kleiner als *end*). Wenn der *end*-Wert nicht angegeben wird, ist dies die obere Grenze des letzten Bin (step), der Daten für jede Reihe enthält.
* *step*: Die Differenz zwischen zwei aufeinanderfolgenden Elementen des *AxisColumn*-Arrays (also der Bingröße).
* *GroupExpression:* Ein Ausdruck für die Spalten, der einen Satz von unterschiedlichen Werten bereitstellt. Hierbei handelt es sich in der Regel um einen Spaltennamen, der bereits einen eingeschränkten Satz von Werten bereitstellt. 
* *MakeSeriesParameters*: Null oder mehr (durch Leerzeichen getrennte) Parameter in Form von *Name* `=` *Wert*, die das Verhalten steuern. Die folgenden Parameter werden unterstützt: 
  
  |Name           |Werte                                        |BESCHREIBUNG                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |Generiert ein Standardergebnis, wenn die Eingabe des make-series-Operators leer ist.|                                

## <a name="returns"></a>Gibt zurück

Die Eingabezeilen werden in Gruppen angeordnet, die dieselben Werte wie die `by` -Ausdrücke und der `bin_at(`*AxisColumn*`, `*step*`, `*start*`)`-Ausdruck aufweisen. Anschließend werden die angegebenen Aggregationsfunktionen über jede Gruppe berechnet, dabei wird eine Zeile für jede Gruppe erzeugt. Das Ergebnis enthält die `by` -Spalten, die Spalte *AxisColumn* und auch mindestens eine Spalte für jedes berechnete Aggregat. (Aggregationen, die mehrere Spalten oder nicht numerische Ergebnisse enthalten, werden nicht unterstützt.)

Dieses Zwischenergebnis weist so viele Zeilen auf, wie es unterschiedliche Kombinationen aus `by` und `bin_at(`*AxisColumn*`, `*step*`, `*start*`)`-Werten gibt.

Schließlich werden die Zeilen aus dem Zwischenergebnis in Gruppen angeordnet, die dieselben Werte wie die `by`-Ausdrücke aufweisen, und alle aggregierten Werte werden in Arrays (Werte vom `dynamic`-Typ) angeordnet. Für jede Aggregation gibt es eine Spalte, die das zugehörige Array mit demselben Namen enthält. Die letzte Spalte in der Ausgabe der range-Funktion mit allen *AxisColumn*-Werten. Ihr Wert wird für alle Zeilen wiederholt. 

Aufgrund der Bins ohne Füllung nach Standardwert weist die sich ergebende Pivottabelle die gleiche Anzahl von Bins (d. h. aggregierte Werte) für alle Reihen auf.  

**Hinweis**

Auch wenn Sie beliebige Ausdrücke für die Aggregation und Gruppierung von Ausdrücken bereitstellen können, ist es effizienter, einfache Spaltennamen zu verwenden.

**Alternativer Syntax**

*T* `| make-series` [*Column* `=`] *Aggregation* [`default` `=` *DefaultValue*] [`,` ...] `on` *AxisColumn* `in` `range(`*start*`,` *stop*`,` *step*`)` [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

Die generierten Reihen aus der alternativen Syntax unterscheiden sich von der Hauptsyntax in zwei Aspekten:
* Der *stop*-Wert ist inklusiv.
* Die Quantisierung der Indexachse wird mit bin() und nicht mit bin_at() generiert, was bedeutet, dass *start* in der generierten Reihe möglicherweise nicht enthalten ist.

Es wird empfohlen, die Hauptsyntax mit make-series und nicht die alternative Syntax zu verwenden.

**Verteilung und Shuffle**

`make-series` unterstützt `summarize` [Shufflekey-Hinweise](shufflequery.md) mit der Syntax hint.shufflekey.

## <a name="list-of-aggregation-functions"></a>Liste der Aggregationsfunktionen

|Funktion|BESCHREIBUNG|
|--------|-----------|
|[any()](any-aggfunction.md)|Gibt einen zufälligen, nicht leeren Wert für die Gruppe zurück.|
|[avg()](avg-aggfunction.md)|Gibt einen Durchschnittswert für die Gruppe zurück.|
|[avgif()](avgif-aggfunction.md)|Gibt einen Durchschnitt mit dem Prädikat der Gruppe zurück.|
|[count()](count-aggfunction.md)|Gibt eine Anzahl der Gruppe zurück.|
|[countif()](countif-aggfunction.md)|Gibt eine Anzahl mit dem Prädikat der Gruppe zurück.|
|[dcount()](dcount-aggfunction.md)|Gibt eine ungefähre eindeutige Anzahl der Gruppenelemente zurück.|
|[dcountif()](dcountif-aggfunction.md)|Gibt eine ungefähre eindeutige Anzahl mit dem Prädikat der Gruppe zurück.|
|[max()](max-aggfunction.md)|Gibt den Höchstwert in der Gruppe zurück.|
|[maxif()](maxif-aggfunction.md)|Gibt den Maximalwert mit dem Prädikat der Gruppe zurück.|
|[min()](min-aggfunction.md)|Gibt den Mindestwert in der Gruppe zurück.|
|[minif()](minif-aggfunction.md)|Gibt einen Minimalwert mit dem Prädikat der Gruppe zurück.|
|[stdev()](stdev-aggfunction.md)|Gibt die Standardabweichung für die Gruppe zurück.|
|[sum()](sum-aggfunction.md)|Gibt die Summe der Elemente in der Gruppe zurück.|
|[sumif()](sumif-aggfunction.md)|Gibt die Summe der Elemente mit dem Prädikat der Gruppe zurück.|
|[variance()](variance-aggfunction.md)|Gibt die Varianz für die Gruppe zurück.|

## <a name="list-of-series-analysis-functions"></a>Liste der Analysefunktionen für Zeitreihen

|Funktion|BESCHREIBUNG|
|--------|-----------|
|[series_fir()](series-firfunction.md)|Wendet einen [FIR-Filter (Finite Impulse Response)](https://en.wikipedia.org/wiki/Finite_impulse_response) an.|
|[series_iir()](series-iirfunction.md)|Wendet einen [IIR-Filter (Infinite Impulse Response)](https://en.wikipedia.org/wiki/Infinite_impulse_response) an.|
|[series_fit_line()](series-fit-linefunction.md)|Ermittelt eine gerade Linie, die die beste Näherung der Eingabe darstellt.|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|Ermittelt eine Linie, die die beste Näherung der Eingabe darstellt, und gibt ein dynamisches Objekt zurück.|
|[series_fit_2lines()](series-fit-2linesfunction.md)|Ermittelt zwei Linien, die die beste Näherung der Eingabe darstellen.|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|Ermittelt zwei Linien, die die beste Näherung der Eingabe sind, und gibt ein dynamisches Objekt zurück.|
|[series_outliers()](series-outliersfunction.md)|Bewertet Anomaliepunkte in einer Reihe.|
|[series_periods_detect()](series-periods-detectfunction.md)|Ermittelt die wichtigsten Punkte, die in einer Zeitreihe vorhanden sind.|
|[series_periods_validate()](series-periods-validatefunction.md)|Überprüft, ob eine Zeitreihe periodische Muster mit den angegebenen Längen enthält.|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|Gibt mehrere Spalten mit den allgemeinen Statistiken zurück (min/max/variance/stdev/average).|
|[series_stats()](series-statsfunction.md)|Generiert einen dynamischen Wert mit den allgemeinen Statistiken (min/max/variance/stdev/average).|
  
## <a name="list-of-series-interpolation-functions"></a>Liste der Reiheninterpolationsfunktionen

|Funktion|BESCHREIBUNG|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|Führt eine Rückwärtsfüllinterpolation von fehlenden Werten in einer Reihe aus.|
|[series_fill_const()](series-fill-constfunction.md)|Ersetzt fehlende Werte in einer Reihe durch einen angegebenen konstanten Wert.|
|[series_fill_forward()](series-fill-forwardfunction.md)|Führt eine Vorwärtsfüllinterpolation von fehlenden Werten in einer Reihe aus.|
|[series_fill_linear()](series-fill-linearfunction.md)|Führt eine lineare Interpolation von fehlenden Werten in einer Reihe aus.|

* Hinweis: Interpolationsfunktionen nehmen standardmäßig `null` als fehlenden Wert an. Geben Sie daher `default=`*double*(`null`) in `make-series` an, wenn Sie beabsichtigen, Interpolationsfunktionen für die Reihe zu verwenden. 

## <a name="example"></a>Beispiel
  
 Eine Tabelle, die Arrays der Zahlen und Durchschnittspreise der einzelnen Früchte von jedem Lieferanten anzeigt, geordnet nach dem Zeitstempel mit dem angegebenen Bereich. Es gibt eine Zeile in der Ausgabe für jede einzelne Kombination von Obst und Lieferant. In den Ausgabespalten werden die Früchte, der Lieferant und die folgenden Arrays angezeigt: count, average und die gesamte Timeline (von 2016-01-01 bis 2016-01-10). Alle Arrays werden nach dem entsprechenden Zeitstempel sortiert, und alle Lücken werden mit Standardwerten aufgefüllt (in diesem Beispiel 0). Alle anderen Eingabespalten werden ignoriert.
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="Drei Tabellen. Die erste Tabelle listet Rohdaten auf, die zweite enthält nur unterschiedliche Kombinationen aus Lieferant, Obst und Datum, und die dritte die Ergebnisse von make-series.":::  

<!-- csl: https://help.kusto.windows.net:443/Samples --> 
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| make-series avg(metric) on timestamp from stime to etime step interval 
```
  
|avg_metric|timestamp|
|---|---|
|[ 4.0, 3.0, 5.0, 0.0, 10.5, 4.0, 3.0, 8.0, 6.5 ]|[ "2017-01-01T00:00:00.0000000Z", "2017-01-02T00:00:00.0000000Z", "2017-01-03T00:00:00.0000000Z", "2017-01-04T00:00:00.0000000Z", "2017-01-05T00:00:00.0000000Z", "2017-01-06T00:00:00.0000000Z", "2017-01-07T00:00:00.0000000Z", "2017-01-08T00:00:00.0000000Z", "2017-01-09T00:00:00.0000000Z" ]|  


Wenn die Eingabe für `make-series` leer ist, führt das Standardverhalten von `make-series` ebenfalls zu einem leeren Ergebnis.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| limit 0
| make-series avg(metric) default=1.0 on timestamp from stime to etime step interval 
| count 
```

|Anzahl|
|---|
|0|


Die Verwendung von `kind=nonempty` in `make-series` führt zu einem nicht leeren Ergebnis der Standardwerte:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| limit 0
| make-series kind=nonempty avg(metric) default=1.0 on timestamp from stime to etime step interval 
```

|avg_metric|timestamp|
|---|---|
|[<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0<br>]|[<br>  "2017-01-01T00:00:00.0000000Z",<br>  "2017-01-02T00:00:00.0000000Z",<br>  "2017-01-03T00:00:00.0000000Z",<br>  "2017-01-04T00:00:00.0000000Z",<br>  "2017-01-05T00:00:00.0000000Z",<br>  "2017-01-06T00:00:00.0000000Z",<br>  "2017-01-07T00:00:00.0000000Z",<br>  "2017-01-08T00:00:00.0000000Z",<br>  "2017-01-09T00:00:00.0000000Z"<br>]|
