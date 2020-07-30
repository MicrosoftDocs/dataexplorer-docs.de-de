---
title: 'Make-Series-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Operator "Make-Series" in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 4f303726532da7ead1c2416f3d485979d045b0b2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346965"
---
# <a name="make-series-operator"></a>make-series-Operator

Erstellt eine Reihe von angegebenen aggregierten Werten auf der angegebenen Achse.

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

## <a name="syntax"></a>Syntax

*T* `| make-series` [*makeseriesparamters*] [*Column* `=` ] *Aggregation* [ `default` `=` *DefaultValue*] [ `,` ...] `on` *axiscolumn* [ `from` *Start*] [ `to` *End*] `step` *Step* [ `by` [*Column* `=` ] *groupexpression* [ `,` ...]]

## <a name="arguments"></a>Argumente

* *Column:* Optionaler Name für eine Ergebnisspalte. Nimmt standardmäßig den vom Ausdruck abgeleiteten Namen an.
* *DefaultValue:* Standardwert, der anstelle von fehlenden Werten verwendet wird. Wenn keine Zeile mit bestimmten Werten von *axiscolumn* und *groupexpression*vorhanden ist, wird dem entsprechenden Element des Arrays in den Ergebnissen ein *DefaultValue*zugewiesen. Wenn *DefaultValue* weggelassen wird, wird 0 angenommen. 
* *Aggregation:* Ein Aufrufe einer [Aggregations Funktion](make-seriesoperator.md#list-of-aggregation-functions) , z `count()` `avg()` . b. oder, mit Spaltennamen als Argumente. Weitere Informationen finden Sie [in der Liste der Aggregations Funktionen](make-seriesoperator.md#list-of-aggregation-functions). Nur Aggregations Funktionen, die numerische Ergebnisse zurückgeben, können mit dem-Operator verwendet werden `make-series` .
* *Axiscolumn:* Eine Spalte, für die die Reihe angeordnet wird. Sie kann als Zeitachse angesehen werden, aber abgesehen `datetime` von allen numerischen Typen werden Sie akzeptiert.
* *Start*: (optional) der niedrige gebundene Wert der *axiscolumn* für jede zu erstellende Reihe. " *Start*", " *End*" und " *Step* " werden verwendet, um ein Array von *axiscolumn* -Werten innerhalb eines angegebenen Bereichs und den angegebenen *Schritt*zu erstellen. Alle *Aggregations* Werte werden in diesem Array angeordnet. Dieses *axiscolumn* -Array ist auch die letzte Ausgabe Spalte in der Ausgabe, die denselben Namen wie " *axiscolumn*" hat. Wenn kein *Startwert* angegeben wird, ist der Start der erste bin (Step), der Daten in jeder Reihe enthält.
* *End*: (optional) der hoch gebundene (nicht inklusiv) Wert von *axiscolumn*. Der letzte Index der Zeitreihe ist kleiner als dieser Wert (und wird mit einem ganzzahligen Vielfachen eines *Schritts* , der kleiner als das *Ende*ist), *beginnen* . Wenn *Endwert* nicht angegeben wird, ist dies die obere Grenze der letzten bin (Step), die Daten pro Reihe enthält.
* *Step*: der Unterschied zwischen zwei aufeinander folgenden Elementen des *axiscolumn* -Arrays (d. h. der bin-Größe).
* *Groupexpression:* Ein Ausdruck für die Spalten, der einen Satz unterschiedlicher Werte bereitstellt. Hierbei handelt es sich in der Regel um einen Spaltennamen, der bereits einen eingeschränkten Satz von Werten bereitstellt. 
* *Makeseriesparameters*: NULL oder mehr (durch Leerzeichen getrennte) Parameter in der Form des *namens* `=` *Werts* , die das Verhalten steuern. Die folgenden Parameter werden unterstützt: 
  
  |Name           |Werte                                        |BESCHREIBUNG                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |Erzeugt ein Standard Ergebnis, wenn die Eingabe des Operators "Make-Series" leer ist.|                                

## <a name="returns"></a>Rückgabe

Die Eingabezeilen werden in Gruppen angeordnet, die dieselben Werte wie die `by` Ausdrücke und den `bin_at(` *AxisColumn* `, ` *step* `, ` *Start* Ausdruck "axiscolumn" aufweisen `)` . Anschließend werden die angegebenen Aggregationsfunktionen über jede Gruppe berechnet, dabei wird eine Zeile für jede Gruppe erzeugt. Das Ergebnis enthält die `by` Spalten *axiscolumn* und auch mindestens eine Spalte für jedes berechnete Aggregat. (Aggregationen, die mehrere Spalten oder nicht numerische Ergebnisse nicht unterstützen.)

Dieses Zwischenergebnis verfügt über so viele Zeilen, wie es unterschiedliche Kombinationen von `by` und `bin_at(` *axiscolumn*- `, ` *Schritt* `, ` *Start* `)` Werten gibt.

Schließlich werden die Zeilen aus dem Zwischenergebnis in Gruppen angeordnet, die dieselben Werte der `by` Ausdrücke aufweisen, und alle aggregierten Werte werden in Arrays (Werte vom `dynamic` Typ) angeordnet. Für jede Aggregation gibt es eine Spalte, die das zugehörige Array mit demselben Namen enthält. Die letzte Spalte in der Ausgabe der Bereichs Funktion mit allen *axiscolumn* -Werten. Der Wert wird für alle Zeilen wiederholt. 

Aufgrund des Standardwerts fehlende Container ausfüllen hat die resultierende Pivottabelle die gleiche Anzahl von Containern (d. h. aggregierte Werte) für alle Reihen.  

**Hinweis**

Obwohl Sie beliebige Ausdrücke für die Aggregations-und Gruppierungs Ausdrücke bereitstellen können, ist es effizienter, einfache Spaltennamen zu verwenden.

**Alternative Syntax**

*T* `| make-series` [*Column* `=` ] *Aggregation* [ `default` `=` *DefaultValue*] [ `,` ...] `on` *axiscolumn* `in` `range(` *Start-Start* `,` *stop* `,` *Schritt* `)` [ `by` [*Column* `=` ] *groupexpression* [ `,` ...]]

Die generierten Reihen aus der alternativen Syntax unterscheiden sich von der Haupt Syntax in zwei Aspekten:
* Der *Endwert* ist inklusiv.
* Das Klassifizieren der Index Achse wird mit bin () und nicht mit bin_at () generiert. Dies bedeutet, dass der *Start* möglicherweise nicht in der generierten Reihe enthalten ist.

Es wird empfohlen, die Haupt Syntax von Make-Series und nicht die alternative Syntax zu verwenden.

**Verteilung und Shuffle**

`make-series`unterstützt `summarize` [shufflekey-Hinweise](shufflequery.md) mithilfe der Syntax Hint. shufflekey.

## <a name="list-of-aggregation-functions"></a>Liste der Aggregations Funktionen

|Funktion|BESCHREIBUNG|
|--------|-----------|
|[Any ()](any-aggfunction.md)|Gibt einen zufälligen, nicht leeren Wert für die Gruppe zurück.|
|[AVG ()](avg-aggfunction.md)|Gibt einen durchschnittlichen Wert in der Gruppe zurück.|
|[count ()](count-aggfunction.md)|Gibt die Anzahl der Gruppe zurück.|
|[countif()](countif-aggfunction.md)|Gibt eine Anzahl mit dem Prädikat der Gruppe zurück.|
|[dcount()](dcount-aggfunction.md)|Gibt eine ungefähre unterschiedliche Anzahl der Group-Elemente zurück.|
|[max()](max-aggfunction.md)|Gibt den Höchstwert in der Gruppe zurück.|
|[min()](min-aggfunction.md)|Gibt den Mindestwert in der Gruppe zurück.|
|[StDev ()](stdev-aggfunction.md)|Gibt die Standardabweichung in der Gruppe zurück.|
|[Sum ()](sum-aggfunction.md)|Gibt die Summe der Elemente in der Gruppe zurück.|
|[variance()](variance-aggfunction.md)|Gibt die Varianz innerhalb der Gruppe zurück.|

## <a name="list-of-series-analysis-functions"></a>Liste der Reihen Analysefunktionen

|Funktion|BESCHREIBUNG|
|--------|-----------|
|[series_fir()](series-firfunction.md)|Wendet einen [begrenzten Impuls Antwort](https://en.wikipedia.org/wiki/Finite_impulse_response) Filter an.|
|[series_iir()](series-iirfunction.md)|Wendet einen [unendlichen Impuls Antwort](https://en.wikipedia.org/wiki/Infinite_impulse_response) Filter an.|
|[series_fit_line()](series-fit-linefunction.md)|Findet eine gerade Linie, die die beste Näherung der Eingabe darstellt.|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|Sucht eine Zeile, die die beste Näherung der Eingabe darstellt und ein dynamisches Objekt zurückgibt.|
|[series_fit_2lines()](series-fit-2linesfunction.md)|Sucht zwei Zeilen, die die beste Näherung der Eingabe sind.|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|Sucht zwei Zeilen, die die beste Näherung der Eingabe sind, und gibt ein dynamisches Objekt zurück.|
|[series_outliers()](series-outliersfunction.md)|Bewertet anomaliepunkte in einer Reihe.|
|[series_periods_detect()](series-periods-detectfunction.md)|Sucht die signifikantesten Zeiträume, die in einer Zeitreihe vorhanden sind.|
|[series_periods_validate()](series-periods-validatefunction.md)|Überprüft, ob eine Zeitreihe periodische Muster mit angegebenen Längen enthält.|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|Gibt mehrere Spalten mit den allgemeinen Statistiken zurück (Min/Max/Varianz/StDev/Average).|
|[series_stats()](series-statsfunction.md)|Generiert einen dynamischen Wert mit der Common Statistics (Min/Max/Varianz/StDev/Average).|
  
## <a name="list-of-series-interpolation-functions"></a>Liste der Reihen Interpolations Funktionen

|Funktion|BESCHREIBUNG|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|Führt eine rückwärts Füll interpolung von fehlenden Werten in einer Reihe aus.|
|[series_fill_const()](series-fill-constfunction.md)|Ersetzt fehlende Werte in einer Reihe durch einen angegebenen konstanten Wert.|
|[series_fill_forward()](series-fill-forwardfunction.md)|Führt die vorwärts Füll interpolung von fehlenden Werten in einer Reihe aus.|
|[series_fill_linear()](series-fill-linearfunction.md)|Führt eine lineare interpolung von fehlenden Werten in einer Reihe aus.|

* Hinweis: Interpolations Funktionen werden standardmäßig `null` als fehlender Wert angenommen. Geben Sie daher `default=` *Double*( `null` ) in an `make-series` , wenn Sie beabsichtigen, Interpolations Funktionen für die Reihe zu verwenden. 

## <a name="example"></a>Beispiel
  
 Eine Tabelle, die Arrays der Zahlen und Durchschnittspreise der einzelnen Früchte von jedem Lieferanten anzeigt, geordnet nach dem Zeitstempel mit dem angegebenen Bereich. Es gibt eine Zeile in der Ausgabe für jede einzelne Kombination von Obst und Lieferant. In den Ausgabespalten werden die Früchte, der Lieferant und die Arrays von angezeigt: count, Average und The All Timeline (von 2016-01-01 bis 2016-01-10). Alle Arrays werden nach dem entsprechenden Zeitstempel sortiert, und alle Lücken werden mit Standardwerten gefüllt (in diesem Beispiel 0). Alle anderen Eingabespalten werden ignoriert.
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="Makeseries":::  

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
|[4,0, 3,0, 5,0, 0,0, 10,5, 4,0, 3,0, 8,0, 6,5]|["2017-01-01t00:00:00.0000000 z", "2017-01-02t00:00:00.0000000 z", "2017-01-03t00:00:00.0000000 z", "2017-01-04t00:00:00.0000000 z", "2017-01-05t00:00:00.0000000 z", "2017-01-06t00:00:00.0000000 z", "2017-01-07t00:00:00.0000000 z", "2017-01-08t00:00:00.0000000 z", "2017-01-09t00:00:00.0000000 z"]|  


Wenn die Eingabe in `make-series` leer ist, erzeugt das Standardverhalten von `make-series` ebenfalls ein leeres Ergebnis.

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


`kind=nonempty`Die Verwendung `make-series` von in führt zu einem nicht leeren Ergebnis der Standardwerte:

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
|[<br>  1,0,<br>  1,0,<br>  1,0,<br>  1,0,<br>  1,0,<br>  1,0,<br>  1,0,<br>  1,0,<br>  1.0<br>]|[<br>  "2017-01-01t00:00:00.0000000 z",<br>  "2017-01-02t00:00:00.0000000 z",<br>  "2017-01-03t00:00:00.0000000 z",<br>  "2017-01-04t00:00:00.0000000 z",<br>  "2017-01-05t00:00:00.0000000 z",<br>  "2017-01-06t00:00:00.0000000 z",<br>  "2017-01-07t00:00:00.0000000 z",<br>  "2017-01-08t00:00:00.0000000 z",<br>  "2017-01-09t00:00:00.0000000 z"<br>]|
