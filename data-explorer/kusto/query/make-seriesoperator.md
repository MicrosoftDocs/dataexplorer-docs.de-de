---
title: Operator der serie - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Make-Series-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 3fa8f1693b56fc0820b9e0ba6b5f03a9363c9b98
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663727"
---
# <a name="make-series-operator"></a>make-series-Operator

Erstellen Sie eine Reihe von angegebenen aggregierten Werten entlang der angegebenen Achse. 

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

**Syntax**

*T* `| make-series` [*MakeSeriesParamters*] [`default` `=` *Spalte* `=`]`,` *Aggregation* [ *DefaultValue*] [ ...] `on` *AxisColumn* `from` *start*[ start`to` ] `step` [ *end*] *step* [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

**Argumente**

* *Column:* Optionaler Name für eine Ergebnisspalte. Nimmt standardmäßig den vom Ausdruck abgeleiteten Namen an.
* *DefaultValue:* Standardwert, der anstelle fehlender Werte verwendet wird. Wenn keine Zeile mit bestimmten Werten von *AxisColumn* und *GroupExpression* vorhanden ist, wird in den Ergebnissen das entsprechende Element des Arrays mit einem *DefaultValue*zugewiesen. Wenn `default =` *DefaultValue* weggelassen wird, wird 0 angenommen. 
* *Aggregation:* Ein Aufruf einer [Aggregationsfunktion](make-seriesoperator.md#list-of-aggregation-functions) wie `count()` oder `avg()`mit Spaltennamen als Argumenten. Siehe [Liste der Aggregationsfunktionen](make-seriesoperator.md#list-of-aggregation-functions). Beachten Sie, dass nur Aggregationsfunktionen, `make-series` die numerische Ergebnisse zurückgeben, mit dem Operator verwendet werden können.
* *AxisColumn:* Eine Spalte, für die die Serie bestellt wird. Es könnte als Zeitachse `datetime` betrachtet werden, aber neben allen numerischen Typen werden akzeptiert.
* *start*: (optional) Der niedrige gebundene Wert der *AxisColumn* für jede der Reihen wird erstellt. *start*, *end* und *step* werden verwendet, um Arrayvon *AxisColumn-Werte* innerhalb eines bestimmten Bereichs zu erstellen und den angegebenen *Schritt zu verwenden.* Alle *Aggregationswerte* werden jeweils nach diesem Array sortiert. Dieses *AxisColumn-Array* ist auch die letzte Ausgabespalte in der Ausgabe mit demselben Namen wie *AxisColumn*. Wenn kein *Startwert* angegeben ist, ist der Start der erste Abschnitt (Schritt), der Daten in jeder Reihe enthält.
* *end*: (optional) Der hohe grenzwertige (nicht inklusive) Wert der *AxisColumn*, der letzte Index der Zeitreihe ist kleiner als dieser Wert (und wird *start* plus ganzzahlig mehrere *Schritte,* die kleiner als *Ende*ist). Wenn der *Endwert* nicht angegeben wird, ist dies die obere Grenze des letzten Abschnitts (Schritt), der Daten pro Reihe enthält.
* *Schritt*: Die Differenz zwischen zwei aufeinander folgenden Elementen des *AxisColumn-Arrays* (d. h. der Abschnittsgröße).
* *GroupExpression:* Ein Ausdruck für die Spalten, der einen Satz von unterschiedlichen Werten bereitstellt. Hierbei handelt es sich in der Regel um einen Spaltennamen, der bereits einen eingeschränkten Satz von Werten bereitstellt. 
* *MakeSeriesParameters*: Null oder mehr (raumgetrennte) Parameter in Form von *Name* `=` *Value,* die das Verhalten steuern. Die folgenden Parameter werden unterstützt: 
  
  |Name           |Werte                                        |BESCHREIBUNG                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |Erzeugt das Standardergebnis, wenn die Eingabe des Make-Series-Operators leer ist|                                

**Rückgabe**

Die Eingabezeilen sind in Gruppen mit `by` den `bin_at(`gleichen Werten des Ausdrucks und des *AxisColumn-Schrittstartausdrucks*`, `*step*`, `*start* `)` angeordnet. Anschließend werden die angegebenen Aggregationsfunktionen über jede Gruppe berechnet, dabei wird eine Zeile für jede Gruppe erzeugt. Das Ergebnis `by` enthält die Spalten, *die AxisColumn-Spalte* und mindestens eine Spalte für jedes berechnete Aggregat. (Aggregation, dass mehrere Spalten oder nicht numerische Ergebnisse nicht unterstützt werden.)

Dieses Zwischenergebnis hat so viele Zeilen, `by` wie `bin_at(`es unterschiedliche Kombinationen von und *AxisColumn*`, `*Schrittstartwerte* `)` *step*`, `gibt.

Schließlich sind die Zeilen aus dem Zwischenergebnis, `by` die in Gruppen mit den gleichen Werten `dynamic` der Ausdrücke angeordnet sind, und alle aggregierten Werte in Arrays (Werte des Typs) angeordnet. Für jede Aggregation gibt es eine Spalte, die ihr Array mit demselben Namen enthält. Die letzte Spalte in der Ausgabe der Bereichsfunktion mit allen *AxisColumn-Werten.* Sein Wert wird für alle Zeilen wiederholt. 

Beachten Sie, dass die resultierende Pivot-Tabelle aufgrund der fehlenden Fülllagerstellen als Standardwert die gleiche Anzahl von Lagerplätzen (d. h. aggregierte Werte) für alle Reihen aufweist.  

**Hinweis**

Obwohl Sie beliebige Ausdrücke sowohl für die Aggregations- als auch für die Gruppierungsausdrücke bereitstellen können, ist es effizienter, einfache Spaltennamen zu verwenden.

**Alternative Syntax**

*T* `| make-series` [*Spalte* `=``default` `=` ] *Aggregation* `,` [ *DefaultValue*] [ ...] `on` *AxisColumn* `in` `,` `,` `by` `=``,` *start* `)` *Column* *GroupExpression* *step* *stop* Startstoppschritt [ [ Spalte ] GroupExpression [ ...]] `range(`

Die generierte Reihe aus der alternativen Syntax unterscheidet sich von der Hauptsyntax in 2 Aspekten:
* Der *Stop-Wert* ist inklusive.
* Binning der Indexachse wird mit bin() generiert und nicht mit bin_at(), was bedeutet, dass *der Start* nicht garantiert in die generierte Reihe einbezogen wird.

Es wird empfohlen, die Hauptsyntax der Make-Serie und nicht die alternative Syntax zu verwenden.

**Verteilung und Shuffle**

`make-series`unterstützt `summarize` [Shufflekey-Hinweise](shufflequery.md) mithilfe der Syntax hint.shufflekey.

## <a name="list-of-aggregation-functions"></a>Liste der Aggregationsfunktionen

|Funktion|BESCHREIBUNG|
|--------|-----------|
|[any()](any-aggfunction.md)|Gibt zufälligen nicht leeren Wert für die Gruppe zurück|
|[avg()](avg-aggfunction.md)|Retuns Durchschnittswert in der gesamten Gruppe|
|[count()](count-aggfunction.md)|Rückgabeanzahl der Gruppe|
|[countif()](countif-aggfunction.md)|Retouren zählen mit dem Prädikat der Gruppe|
|[dcount()](dcount-aggfunction.md)|Gibt ungefähre eindeutige Anzahl der Gruppenelemente zurück|
|[max()](max-aggfunction.md)|Gibt den Maximalwert in der Gruppe zurück|
|[min()](min-aggfunction.md)|Gibt den Mindestwert in der Gruppe zurück|
|[stdev()](stdev-aggfunction.md)|Gibt die Standardabweichung über die Gruppe hinweg zurück|
|[sum()](sum-aggfunction.md)|Gibt die Summe der Elemente zurück, die die Gruppe|
|[variance()](variance-aggfunction.md)|Gibt die Varianz in der Gruppe zurück|

## <a name="list-of-series-analysis-functions"></a>Liste der Reihenanalysefunktionen

|Funktion|BESCHREIBUNG|
|--------|-----------|
|[series_fir()](series-firfunction.md)|Wendet [Finite Impulse Response](https://en.wikipedia.org/wiki/Finite_impulse_response) Filter an|
|[series_iir()](series-iirfunction.md)|Wendet [den Filtern "Unendliche Impulsantwort"](https://en.wikipedia.org/wiki/Infinite_impulse_response) an|
|[series_fit_line()](series-fit-linefunction.md)|Findet eine gerade Linie, die die beste Annäherung an den Eingang ist|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|Sucht eine Zeile, die die beste Annäherung an die Eingabe ist, und gibt dynamisches Objekt zurück|
|[series_fit_2lines()](series-fit-2linesfunction.md)|Sucht zwei Zeilen, die die beste Annäherung an den Eingang ist|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|Sucht zwei Zeilen, die die beste Annäherung an die Eingabe ist, und gibt dynamisches Objekt zurück|
|[series_outliers()](series-outliersfunction.md)|Erzielt Anomaliepunkte in einer Serie|
|[series_periods_detect()](series-periods-detectfunction.md)|Sucht die wichtigsten Zeiträume, die in einer Zeitreihe vorhanden sind|
|[series_periods_validate()](series-periods-validatefunction.md)|Überprüft, ob eine Zeitreihe periodische Muster bestimmter Längen enthält|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|Geben Sie mehrere Spalten mit den gemeinsamen Statistiken zurück (min/max/variance/stdev/average)|
|[series_stats()](series-statsfunction.md)|Generiert einen dynamischen Wert mit den gemeinsamen Statistiken (min/max/variance/stdev/average)|
  
## <a name="list-of-series-interpolation-functions"></a>Liste der Serieninterpolationsfunktionen
|Funktion|BESCHREIBUNG|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|Führt die Rückwärtsfüllinterpolation fehlender Werte in einer Reihe durch|
|[series_fill_const()](series-fill-constfunction.md)|Ersetzt fehlende Werte in einer Reihe durch einen angegebenen konstanten Wert|
|[series_fill_forward()](series-fill-forwardfunction.md)|Führt die Vorwärtsfüllinterpolation fehlender Werte in einer Reihe durch|
|[series_fill_linear()](series-fill-linearfunction.md)|Führt lineare Interpolation fehlender Werte in einer Reihe durch|

* Hinweis: Interpolationsfunktionen werden `null` standardmäßig als fehlender Wert angenommen. Daher wird empfohlen, `default=` *Double*(`null`) in `make-series` anzugeben, wenn Sie Interpolationsfunktionen für die Serie verwenden möchten. 

## <a name="example"></a>Beispiel
  
 Eine Tabelle, die Arrays der Zahlen und Durchschnittspreise der einzelnen Früchte von jedem Lieferanten nach dem Zeitstempel mit dem angegebenen Bereich anzeigt. Es gibt eine Reihe in der Ausgabe für jede einzelne Kombination von Obst und Lieferant. Die Ausgabespalten zeigen die Früchte, Lieferanten und Arrays von: Zählen, Durchschnitt und die gesamte Zeitlinie (von 2016-01-01 bis 2016-01-10). Alle Arrays werden nach dem jeweiligen Zeitstempel sortiert und alle Lücken werden mit Standardwerten gefüllt (in diesem Beispiel 0). Alle anderen Eingabespalten werden ignoriert.
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/aggregations/makeseries.png" alt-text="Makeseries":::  
  
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
|[ 4.0, 3.0, 5.0, 0.0, 10.5, 4.0, 3.0, 8.0, 6.5 ]|[ "2017-01-01T00:00:00.0000000Z", "2017-01-02T00:00:00.0000000Z", "2017-01 -03T00:00:00.0000000Z", "2017-01-04T00:00:00.0000000Z", "2017-01-05T00:0 00:00.0000000Z", "2017-01-06T00:00:00.00000000Z", "2017-01-07T00:00:00.000 00000Z", "2017-01-08T00:00:00.00000000Z", "2017-01-09T00:00:00.0000000Z" ]|  


Wenn die `make-series` Eingabe leer ist, `make-series` erzeugt das Standardverhalten von auch ein leeres Ergebnis.

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


Die `kind=nonempty` `make-series` Verwendung von in führt zu einem nicht leeren Ergebnis der Standardwerte:

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