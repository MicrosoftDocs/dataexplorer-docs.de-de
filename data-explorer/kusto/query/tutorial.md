---
title: 'Tutorial: Kusto-Abfragen in Azure Data Explorer und Azure Monitor'
description: Dieses Tutorial beschreibt, wie Sie Abfragen in der Kusto-Abfragesprache verwenden, um Anforderungen gängiger Abfragen in Azure Data Explorer und Azure Monitor zu erfüllen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d66b8d949e04f6cd672078989c9d047729c9596b
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360080"
---
# <a name="tutorial-use-kusto-queries-in-azure-data-explorer-and-azure-monitor"></a>Tutorial: Verwenden von Kusto-Abfragen in Azure Data Explorer und Azure Monitor

::: zone pivot="azuredataexplorer"

Die beste Möglichkeit, um mehr über die Kusto-Abfragesprache zu erfahren, besteht darin, einige grundlegende Abfragen zu untersuchen, um ein „Gefühl“ für die Sprache zu bekommen. Es wird empfohlen, eine [Datenbank mit ein paar Beispieldaten](https://help.kusto.windows.net/Samples) zu verwenden. Die in diesem Tutorial gezeigten Abfragen sollten mit dieser Datenbank ausgeführt werden. Die `StormEvents`-Tabelle in der-Beispieldatenbank enthält Informationen zu Stürmen, die in den USA aufgetreten sind.

## <a name="count-rows"></a>Zeilen zählen

Unsere Beispieldatenbank enthält eine Tabelle namens `StormEvents`. Um herauszufinden, wie groß die Tabelle ist, übergeben wir ihren Inhalt per Pipe an einen Operator, der schlicht die Zeilen in der Tabelle zählt. 

*Syntaxhinweis*: Eine Abfrage ist eine Datenquelle (normalerweise ein Tabellenname), optional gefolgt von einem oder mehreren Paaren des Pipe-Zeichens (senkrechter Strich) und einem tabellarischen Operator.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

So sieht die Ausgabe aus:

|Anzahl|
|-----|
|59066|
    
Weitere Informationen finden Sie unter [count-Operator](./countoperator.md).

## <a name="select-a-subset-of-columns-project"></a>Auswählen einer Teilmenge von Spalten: *project*

Verwenden Sie [project](./projectoperator.md), um nur die gewünschten Spalten auszuwählen. Weitere Informationen finden Sie im folgenden Beispiel, in dem die beiden Operatoren [project](./projectoperator.md) und [take](./takeoperator.md) verwendet werden.

## <a name="filter-by-boolean-expression-where"></a>Filtern nach booleschem Ausdruck: *where*

Sehen wir uns nur `flood`-Ereignisse in `California` im Februar 2007 an:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

So sieht die Ausgabe aus:

|StartTime|EndTime|State|EventType|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|CALIFORNIA|Hochwasser|Ein Frontensystem, das sich über das südliche San-Joaquin-Tal bewegte, brachte in den frühen Morgenstunden des 19. kurze Perioden starken Regens in das westliche Kern County. Geringfügige Überschwemmungen wurden für den State Highway 166 bei Taft gemeldet.|

## <a name="show-n-rows-take"></a>Anzeigen von *n* Zeilen: *take*

Sehen Sie wir uns ein paar Daten an. Was befindet sich in einer zufälligen Stichprobe von fünf Zeilen?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

So sieht die Ausgabe aus:

|StartTime|EndTime|EventType|State|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Starker Regen|FLORIDA|Bis zu 23 cm Regen fielen in einem Zeitraum von 24 Stunden in Teilen der Küste von Volusia County.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Tornado|FLORIDA|Ein Tornado traf in der Stadt Eustis am nördlichen Ende des West Crooked Lake auf Land. Der Tornado wuchs schnell auf Stärke EF1 an, während er in nordnordwestlicher Richtung durch Eustis zog. Seine Spur blieb knapp unter 3,2 km und hatte eine maximale Breite von 300 Metern.  Der Tornado zerstörte 7 Häuser. 27 Häuser wurden schwer beschädigt, 81 Häuser meldeten geringfügige Schäden. Es gab keine schweren Verletzten, und der Eigentumsschaden wurde auf USD 6,2 Millionen beziffert.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|Wasserhose|ATLANTIC SOUTH|Südöstlich von Melbourne Beach bildete sich im Atlantik eine Wasserhose, die sich kurz darauf in Richtung Ufer bewegte.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Sturm|MISSISSIPPI|Zahlreiche große Bäume wurden umgelegt, wobei einige Stromleitungen beschädigt wurden. Im östlichen Adams County kam es zu Schäden.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Sturm|GEORGIA|Die Bezirksleitstelle berichtete, dass mehrere Bäume entlang der Quincey Batten Loop in der Nähe der State Road 206 umgelegt wurden. Die Kosten für die Beseitigung der Bäume wurden geschätzt.|

[take](./takeoperator.md) zeigt jedoch Zeilen aus der Tabelle in keiner bestimmten Reihenfolge, also sortieren wir sie. ([limit](./takeoperator.md) ist ein Alias für [take](./takeoperator.md) und hat denselben Effekt.)

## <a name="order-results-sort-top"></a>Sortieren von Ergebnissen: *sort*, *top*

* *Syntaxhinweis*: Einige Operatoren besitzen Parameter, die mit Schlüsselwörtern wie `by` eingeführt werden.
* Im folgenden Beispiel sortiert `desc` Ergebnisse in absteigender, und `asc` sortiert Ergebnisse in aufsteigender Reihenfolge.

Zeige mir die ersten *n* Zeilen, sortiert nach einer bestimmten Spalte:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

So sieht die Ausgabe aus:

|StartTime|EndTime|EventType|State|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Wintersturm|MICHIGAN|Dieses schwere Schneeereignis setzte sich bis in die frühen Morgenstunden am Neujahrstag fort.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Wintersturm|MICHIGAN|Dieses schwere Schneeereignis setzte sich bis in die frühen Morgenstunden am Neujahrstag fort.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Wintersturm|MICHIGAN|Dieses schwere Schneeereignis setzte sich bis in die frühen Morgenstunden am Neujahrstag fort.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Starker Wind|CALIFORNIA|In den Bergen von Ventura County wurden Winde aus Nord bis Nordost mit Böen bis zu 95 km/h gemeldet.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Starker Wind|CALIFORNIA|Der Warm Springs RAWs-Sensor hat nördliche Windböen mit bis zu 95 km/h gemeldet.|

Sie können dieselben Ergebnisse erzielen, indem Sie [sort](./sortoperator.md) und dann [take](./takeoperator.md) verwenden:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="compute-derived-columns-extend"></a>Berechnen abgeleiteter Spalten: *extend*

Erstellen einer neuen Spalte durch Berechnen eines Werts in jeder Zeile:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

So sieht die Ausgabe aus:

|StartTime|EndTime|Duration|EventType|State|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Starker Regen|FLORIDA|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Tornado|FLORIDA|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|Wasserhose|ATLANTIC SOUTH|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Sturm|MISSISSIPPI|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Sturm|GEORGIA|

Es ist möglich, einen Spaltennamen wiederzuverwenden und derselben Spalte ein Berechnungsergebnis zuzuweisen.

Beispiel:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

So sieht die Ausgabe aus:

|x|y|
|---|---|
|3|1|

[Skalarausdrücke](./scalar-data-types/index.md) können alle gängigen Operatoren enthalten (`+`, `-`, `*`, `/`, `%`), und ferner steht eine Reihe nützlicher Funktionen zur Verfügung.

## <a name="aggregate-groups-of-rows-summarize"></a>Aggregieren von Zeilengruppen: *summarize*

Zählen der Anzahl der Ereignisse, die in jedem Bundesstaat auftreten:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[summarize](./summarizeoperator.md) gruppiert Zeilen, die denselben Wert in der `by`-Klausel haben, und verwendet dann eine Aggregationsfunktion (z. B.`count`), um jede Gruppe zu einer einzelnen Zeile zu kombinieren. In diesem Fall gibt es für jeden Bundesstaat eine Zeile, und eine Spalte für die Anzahl der Zeilen in diesem Bundesstaat.

Es steht eine Reihe von [Aggregationsfunktionen](./summarizeoperator.md#list-of-aggregation-functions) zur Verfügung. Sie können mehrere Aggregationsfunktionen in einem `summarize`-Operator verwenden, um mehrere berechnete Spalten zu erzeugen. Beispielsweise könnten wir die Anzahl der Stürme in jedem Bundesstaat sowie eine Summe eines einzigartigen Typs von Stürmen pro Bundesstaat erhalten. Anschließend könnten wir [top](./topoperator.md) verwenden, um die meisten von Stürmen betroffenen Bundesstaaten zu erhalten:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

So sieht die Ausgabe aus:

|State|StormCount|TypeOfStorms|
|---|---|---|
|TEXAS|4701|27|
|KANSAS|3166|21|
|IOWA|2337|19|
|ILLINOIS|2022|23|
|MISSOURI|2016|20|

In den Ergebnissen eines `summarize`-Operators:

* Jede Spalte ist in `by` benannt.
* Jeder berechnete Ausdruck hat eine Spalte.
* Jede Kombination von `by`-Werten hat eine Zeile.

## <a name="summarize-by-scalar-values"></a>Zusammenfassen nach Skalarwerten

Sie können Skalarwerte (numerisch, Uhrzeit oder Intervall) in der `by`-Klausel verwenden, aber Sie sollten die Werte in Intervallen platzieren, indem Sie die [bin ()](./binfunction.md)-Funktion verwenden:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

Die Abfrage reduziert alle Zeitstempel auf Intervalle von einem Tag:

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

Die [bin()](./binfunction.md)-Funktion ist mit der [floor()](./floorfunction.md)-Funktion in vielen Sprachen identisch. Sie reduziert jeden Wert auf das nächste Vielfache des von Ihnen angegebenen Rundungswerts, damit [summarize](./summarizeoperator.md) die Zeilen den Gruppen zuweisen kann.

<a name="displaychartortable"></a>
## <a name="display-a-chart-or-table-render"></a>Anzeigen eines Diagramms oder einer Tabelle: *render*

Sie können zwei Spalten projizieren und sie dann als x-Achse und y-Achse eines Diagramms verwenden:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="Screenshot, der ein Säulendiagramm der Anzahl von Sturmereignissen nach Bundesstaat zeigt.":::

Obwohl wir im `project`-Vorgang `mid` entfernt haben, benötigen wir es weiterhin, wenn das Diagramm die Bundesstaaten in dieser Reihenfolge anzeigen soll.

Streng genommen ist `render` eher eine Funktion des Clients anstatt Bestandteil der Abfragesprache. Dennoch ist es in die Sprache integriert und eignet sich für das Visualisieren Ihrer Ergebnisse.


## <a name="timecharts"></a>Zeitdiagramme

Wenn wir nun zu numerischen Intervallen zurückkehren, zeigen wir eine Zeitreihe an:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="Screenshot eines Liniendiagramms von Ereignissen in Intervallen nach Zeit.":::

## <a name="multiple-series"></a>Mehrere Reihen

Verwenden Sie mehrere Werte in einer `summarize by` -Klausel, um eine separate Zeile für jede Kombination von Werten zu erstellen:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="Screenshot, der eine Tabellenanzahl nach Quelle zeigt.":::

Fügen Sie im vorangehenden Beispiel einfach den Begriff `render` hinzu: `| render timechart`.

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="Screenshot, der eine Tabellenanzahl nach Quelle zeigt.":::

Beachten Sie, dass `render timechart` die erste Spalte als x-Achse verwendet und dann die anderen Spalten als separate Linien anzeigt.

## <a name="daily-average-cycle"></a>Durchschnittlicher Tageszyklus

Wie variiert die Aktivität im Verlauf eines durchschnittlichen Tages?

Zählen Sie die Ereignisse nach Zeit, gerundet auf 1 Tag, und in Stundenintervallen. Hier verwenden wir `floor` anstelle von `bin`:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="Screenshot, der eine Zeitdiagrammanzahl nach Quelle zeigt.":::

Derzeit werden Dauerangaben von `render` nicht ordnungsgemäß bezeichnet, aber wir könnten stattdessen `| render columnchart` verwenden:

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="Screenshot, der eine Säulendiagrammanzahl nach Stunde anzeigt.":::

## <a name="compare-multiple-daily-series"></a>Vergleichen mehrerer täglicher Reihen

Wie variiert die Aktivität im Verlauf des Tages in unterschiedlichen Bundesstaaten?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="Screenshot eines Zeitdiagramms nach Stunde und Bundesstaat.":::

Dividieren Sie durch `1h`, um die x-Achsenwerte in eine Stundenzahl anstelle einer Dauer umzuwandeln:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="Screenshot, der ein Säulendiagramm nach Stunde und Bundesstaat zeigt.":::

## <a name="join-data-types"></a>Verknüpfen von Datentypen

Wie würden Sie zwei spezifische Ereignistypen suchen und dabei herausfinden, in welchem Bundesstaat jeder davon aufgetreten ist?

Sie können Sturmereignisse des ersten `EventType` und zweiten `EventType` abrufen und dann die beiden Sätze über `State` verknüpfen:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tutorial/join-events-lightning-avalanche.png" alt-text="Screenshot, der das Verknüpfen der Ereignisse „Blitz“ und „Lawine“ zeigt.":::

## <a name="user-session-example-of-join"></a>Benutzersitzungsbeispiel für *join*

In diesem Abschnitt wird die `StormEvents`-Tabelle nicht verwendet.

Angenommen, Sie verfügen über Daten, die Ereignisse umfassen, die den Beginn und das Ende jeder Benutzersitzung mit einer eindeutigen ID für jede Sitzung markieren. 

Wie würden Sie feststellen, wie lange die einzelnen Benutzersitzungen dauern?

Mit `extend` können Sie einen Alias für die beiden Zeitstempel angeben und dann die Sitzungsdauer berechnen:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
Events
| where eventName == "session_started"
| project start_time = timestamp, stop_time, country, session_id
| join ( Events
    | where eventName == "session_ended"
    | project stop_time = timestamp, session_id
    ) on session_id
| extend duration = stop_time - start_time
| project start_time, stop_time, country, duration
| take 10
```

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="Screenshot einer Tabelle mit Ergebnissen für Benutzersitzungen mit extend.":::

Es ist eine bewährte Vorgehensweise, `project` zu verwenden, um vor dem Verknüpfen nur die Spalten auszuwählen, die Sie benötigen. In denselben Klauseln benennen Sie die Spalte `timestamp` um.

## <a name="plot-a-distribution"></a>Darstellen einer Verteilung

Um wieder zu der `StormEvents`-Tabelle zurückzukehren, wie viele Stürme unterschiedlicher Längen sind vorhanden?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m)
| sort by duration asc
| render timechart
```

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="Screenshot der Zeitdiagrammergebnisse für die Ereignisanzahl nach Dauer.":::

Alternativ können Sie auch `| render columnchart` verwenden:

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="Screenshot eines Säulendiagramms für das Ereignisanzahl-Zeitdiagramm nach Dauer.":::

## <a name="percentiles"></a>Perzentile

Welche Bereiche von Dauerwerten finden wir in verschiedene Prozentsätzen von Stürmen?

Um diese Informationen zu erhalten, verwenden Sie die vorherige Abfrage, ersetzen aber `render` durch:

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

In diesem Fall haben wir keine `by`-Klausel verwendet, sodass die Ausgabe eine einzelne Zeile ist:

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" lightbox="images/tutorial/summarize-percentiles-duration.png" alt-text="Screenshot einer Tabelle mit Ergebnissen für summarize-Quantile nach Dauer.":::

In der Ausgabe erkennen wir Folgendes:

* 5 % der Stürme dauern weniger als 5 Minuten.
* 50 % der Stürme dauerten weniger als eine Stunde und 25 Minuten.
* 95 % der Stürme dauerten weniger als zwei Stunden und 50 Minuten.

Um eine gesonderte Aufschlüsselung für jeden Bundesstaat zu erhalten, verwenden Sie die Spalte `state` separat mit beiden `summarize`-Operatoren:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m), State
| sort by duration asc
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="Tabelle der summarize-Quantildauern nach Bundesstaat.":::

## <a name="assign-a-result-to-a-variable-let"></a>Zuweisen eines Ergebnisses zu einer Variablen: *let*

Verwenden Sie [let](./letstatement.md), um die einzelnen Teile des Abfrageausdrucks im vorangehenden `join`-Beispiel zu separieren. Die Ergebnisse sind wie folgt unverändert:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let LightningStorms = 
    StormEvents
    | where EventType == "Lightning";
let AvalancheStorms = 
    StormEvents
    | where EventType == "Avalanche";
LightningStorms 
| join (AvalancheStorms) on State
| distinct State
```
> [!TIP]
> Fügen Sie im Kusto-Explorer, um die gesamte Abfrage auszuführen, keine leeren Zeilen zwischen den Teilen der Abfrage hinzu.

## <a name="combine-data-from-several-databases-in-a-query"></a>Kombinieren von Daten aus mehreren Datenbanken in einer Abfrage

In der folgenden Abfrage muss sich die `Logs`-Tabelle in Ihrer Standarddatenbank befinden:

```kusto
Logs | where ...
```

Verwenden Sie die folgende Syntax, um auf eine Tabelle in einer anderen Datenbank zuzugreifen:

```kusto
database("db").Table
```

Wenn Sie z. B. Datenbanken mit den Namen `Diagnostics` und `Telemetry` haben und einige der Daten in den beiden Tabellen korrelieren möchten, können Sie die folgende Abfrage verwenden (vorausgesetzt, `Diagnostics` ist Ihre Standarddatenbank):

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

Verwenden Sie diese Abfrage, wenn Ihre Standarddatenbank `Telemetry` ist:

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
Bei den vorherigen beiden Abfragen wird davon ausgegangen, dass sich beide Datenbanken im Cluster befinden, mit dem Sie gerade verbunden sind. Wenn sich die `Telemetry`-Datenbank in einem Cluster mit dem Namen *TelemetryCluster.kusto.windows.net* befand, verwenden Sie diese Abfrage, um darauf zuzugreifen:

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> Wenn der Cluster angegeben wird, ist die Datenbank obligatorisch.

Weitere Informationen zum Kombinieren von Daten aus mehreren Datenbanken in einer Abfrage finden Sie unter [datenbankübergreifende Abfragen](cross-cluster-or-database-queries.md).

## <a name="next-steps"></a>Nächste Schritte

- Anzeigen von Codebeispielen für die [Kusto-Abfragesprache](samples.md?pivots=azuredataexplorer).

::: zone-end

::: zone pivot="azuremonitor"

Die beste Möglichkeit, um mehr über die Kusto-Abfragesprache zu erfahren, besteht darin, einige grundlegende Abfragen zu untersuchen, um ein „Gefühl“ für die Sprache zu bekommen. Diese Abfragen ähneln Abfragen, die im Azure Data Explorer-Tutorial verwendet werden, aber sie verwenden stattdessen Daten aus allgemeinen Tabellen in einem Azure Log Analytics-Arbeitsbereich. 

Führen Sie diese Abfragen aus, indem Sie Log Analytics im Azure-Portal verwenden. Log Analytics ist ein Tool, das Sie zum Schreiben von Protokollabfragen verwenden können. Verwenden Sie Protokolldaten in Azure Monitor, und werten Sie dann die Ergebnisse der Protokollabfrage aus. Wenn Sie mit Log Analytics nicht vertraut sind, absolvieren Sie das [Log Analytics-Tutorial](/azure/azure-monitor/log-query/log-analytics-tutorial).

Alle Abfragen in diesem Tutorial verwenden die [Log Analytics-Demoumgebung](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade). Sie können Ihre eigene Umgebung verwenden, aber Ihnen fehlen dann möglicherweise einige der hier verwendeten Tabellen. Da die Daten in der Demoumgebung nicht statisch sind, können die Ergebnisse Ihrer Abfragen geringfügig von den hier gezeigten Ergebnissen abweichen.


## <a name="count-rows"></a>Zeilen zählen

Die [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics)-Tabelle enthält Leistungsdaten, die von Erkenntnissen wie Azure Monitor für VMs und Azure Monitor für Container erfasst werden. Um herauszufinden, wie groß die Tabelle ist, übergeben wir ihren Inhalt per Pipe an einen Operator, der schlicht die Zeilen zählt.

Eine Abfrage ist eine Datenquelle (normalerweise ein Tabellenname), optional gefolgt von einem oder mehreren Paaren des Pipe-Zeichens (senkrechter Strich) und einem tabellarischen Operator. In diesem Fall werden alle Datensätze aus der `InsightsMetrics`-Tabelle zurückgegeben und dann an den [count-Operator](./countoperator.md) gesendet. Der `count`-Operator zeigt die Ergebnisse an, weil der Operator der letzte Befehl in der Abfrage ist.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

So sieht die Ausgabe aus:

|Anzahl|
|-----|
|1\.263.191|
    

## <a name="filter-by-boolean-expression-where"></a>Filtern nach booleschem Ausdruck: *where*

Die [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity)-Tabelle enthält Einträge aus dem Azure-Aktivitätsprotokoll, die Erkenntnisse in alle Ereignisse auf Abonnement- oder Verwaltungsgruppenebene vermitteln, die in Azure aufgetreten sind. Sehen wir uns nur `Critical`-Einträge in einer bestimmten Woche an.

Der [where](./whereoperator.md)-Operator ist gängig in der Kusto-Abfragesprache. `where` filtert eine Tabelle zu Zeilen, die bestimmten Kriterien entsprechen. Das folgende Beispiel verwendet mehrere Befehle. Zuerst ruft die Abfrage alle Datensätze für die Tabelle ab. Anschließend filtert sie die Daten nach nur Einträgen, die in dem Zeitbereich liegen. Schließlich filtert sie diese Ergebnisse nach nur Datensätzen, die die Stufe `Critical` haben.

> [!NOTE]
> Zusätzlich zur Angabe eines Filters in Ihrer Abfrage mithilfe der `TimeGenerated`-Spalte können Sie den Zeitbereich in Log Analytics angeben. Weitere Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope).

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

:::image type="content" source="images/tutorial/azure-monitor-where-results.png" lightbox="images/tutorial/azure-monitor-where-results.png" alt-text="Screenshot mit den Ergebnissen des where-Operatorbeispiels.":::

## <a name="select-a-subset-of-columns-project"></a>Auswählen einer Teilmenge von Spalten: *project*

Verwenden Sie [project](./projectoperator.md), um nur die gewünschten Spalten einzuschließen. Aufbauend auf dem vorherigen Beispiel begrenzen wir die Ausgabe auf bestimmte Spalten:

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

:::image type="content" source="images/tutorial/azure-monitor-project-results.png" lightbox="images/tutorial/azure-monitor-project-results.png" alt-text="Screenshot mit den Ergebnissen des project-Operatorbeispiels.":::

## <a name="show-n-rows-take"></a>Anzeigen von *n* Zeilen: *take*

[NetworkMonitoring](/azure/azure-monitor/reference/tables/networkmonitoring) enthält Überwachungsdaten für virtuelle Azure-Netzwerke. Verwenden Sie nun den [take](./takeoperator.md)-Operator, um sich zehn zufällige Beispielzeilen in dieser Tabelle anzusehen. [take](./takeoperator.md) zeigt eine bestimmte Anzahl von Zeilen aus einer Tabelle in keiner bestimmten Reihenfolge:

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-take-results.png" lightbox="images/tutorial/azure-monitor-take-results.png" alt-text="Screenshot mit den Ergebnissen des take-Operatorbeispiels.":::

## <a name="order-results-sort-top"></a>Sortieren von Ergebnissen: *sort*, *top*

Anstelle zufälliger Datensätze können wir die letzten fünf Datensätze zurückgeben, indem wir zuerst nach Zeit sortieren:

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

Sie können dieses exakte Verhalten erzielen, indem Sie stattdessen den [top](./topoperator.md)-Operator verwenden: 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-top-results.png" lightbox="images/tutorial/azure-monitor-top-results.png" alt-text="Screenshot mit den Ergebnissen des top-Operatorbeispiels.":::

## <a name="compute-derived-columns-extend"></a>Berechnen abgeleiteter Spalten: *extend*

Der [extend](./projectoperator.md)-Operator ähnelt [project](./projectoperator.md), aber er fügt dem Satz von Spalten hinzu, anstatt ihn zu ersetzen. Sie können beide Operatoren verwenden, um eine neue Spalte auf Grundlage einer Berechnung für jede Zeile zu erstellen.

Die [Perf](/azure/azure-monitor/reference/tables/perf)-Tabelle enthält Leistungsdaten, die von virtuellen Computern erfasst werden, auf denen der Log Analytics-Agent ausgeführt wird. 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

:::image type="content" source="images/tutorial/azure-monitor-extend-results.png" lightbox="images/tutorial/azure-monitor-extend-results.png" alt-text="Screenshot mit den Ergebnissen des extend-Operatorbeispiels.":::

## <a name="aggregate-groups-of-rows-summarize"></a>Aggregieren von Zeilengruppen: *summarize*

Der [summarize](./summarizeoperator.md)-Operator gruppiert Zeilen mit denselben Werten in der `by`-Klausel. Anschließend verwendet er eine Aggregationsfunktion wie `count`, um jede Gruppe in einer einzelnen Zeile zu kombinieren. Es steht eine Reihe von [Aggregationsfunktionen](./summarizeoperator.md#list-of-aggregation-functions) zur Verfügung. Sie können mehrere Aggregationsfunktionen in einem `summarize`-Operator verwenden, um mehrere berechnete Spalten zu erzeugen. 

Die [SecurityEvent](/azure/azure-monitor/reference/tables/securityevent)-Tabelle enthält Sicherheitsereignisse wie Anmeldungen und Prozesse, die auf überwachten Computern gestartet wurden. Sie können zählen, wie viele Ereignisse jeder Ebene auf den einzelnen Computern aufgetreten sind. In diesem Beispiel wird für jede Kombination aus Computer und Ebene eine Zeile erstellt. Eine Spalte enthält die Anzahl der Ereignisse.

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-count-results.png" lightbox="images/tutorial/azure-monitor-summarize-count-results.png" alt-text="Screenshot mit den Ergebnissen des „summarize count“-Operatorbeispiels.":::

## <a name="summarize-by-scalar-values"></a>Zusammenfassen nach Skalarwerten

Sie können nach Skalarwerten wie Zahlen und Zeitwerten aggregieren, aber Sie sollten die [bin()](./binfunction.md)-Funktion verwenden, um Zeilen in getrennte Datensätze zu gruppieren. Wenn Sie beispielsweise `TimeGenerated` aggregieren, erhalten Sie eine Zeile für fast jeden Zeitwert. Verwenden Sie `bin()`, um diese Werte in Stunde oder Tag zu konsolidieren.

Die [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics)-Tabelle enthält Leistungsdaten, die von Erkenntnissen wie Azure Monitor für VMs und Azure Monitor für Container erfasst werden. Die folgende Abfrage zeigt die stündliche durchschnittliche Prozessorauslastung für mehrere Computer:

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-avg-results.png" lightbox="images/tutorial/azure-monitor-summarize-avg-results.png" alt-text="Screenshot mit den Ergebnissen des avg-Operatorbeispiels.":::

## <a name="display-a-chart-or-table-render"></a>Anzeigen eines Diagramms oder einer Tabelle: *render*

Der [render](./renderoperator.md?pivots=azuremonitor)-Operator gibt an, wie die Ausgabe der Abfrage gerendert wird. Log Analytics rendert die Ausgabe standardmäßig als Tabelle. Sie können nach der Ausführung der Abfrage verschiedene Diagrammtypen auswählen. Der `render`-Operator lässt sich nützlicherweise in Abfragen aufnehmen, in denen normalerweise ein bestimmter Diagrammtyp bevorzugt wird.

Das folgende Beispiel zeigt die stündliche durchschnittliche Prozessorauslastung für einen einzelnen Computer. Die Ausgabe wird als Zeitdiagramm gerendert.

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-results.png" lightbox="images/tutorial/azure-monitor-render-results.png" alt-text="Screenshot mit den Ergebnissen des render-Operatorbeispiels.":::

## <a name="work-with-multiple-series"></a>Arbeiten mit mehreren Reihen

Verwenden Sie mehrere Werte in einer `summarize by`-Klausel verwenden, zeigt das Diagramm für jeden Satz von Werten eine gesonderte Reihe an:

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-multiple-results.png" lightbox="images/tutorial/azure-monitor-render-multiple-results.png" alt-text="Screenshot mit den Ergebnissen des render-Operatorbeispiels mit mehreren Reihen.":::

## <a name="join-data-from-two-tables"></a>Verknüpfen von Daten aus zwei Tabellen

Was ist, wenn Sie Daten aus zwei Tabellen in einer einzelnen Abfrage abrufen müssen? Sie können den [join](./joinoperator.md?pivots=azuremonitor)-Operator verwenden, um Zeilen aus mehreren Tabellen in einem einzigen Resultset zu kombinieren. Jede Tabelle muss eine Spalte mit einem übereinstimmenden Wert aufweisen, damit der Verknüpfungsvorgang (join) weiß, welche Zeilen zugeordnet werden müssen.

[VMComputer](/azure/azure-monitor/reference/tables/vmcomputer) ist eine Tabelle, die Azure Monitor für VMs verwendet, um Details zu den von überwachten virtuellen Computern zu speichern. [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) enthält Leistungsdaten, die von diesen virtuellen Computern erfasst werden. Ein in *InsightsMetrics* erfasster Wert ist verfügbarer Arbeitsspeicher, aber nicht der Prozentsatz des verfügbaren Arbeitsspeichers. Um den Prozentsatz zu berechnen, benötigen wir den physischen Arbeitsspeicher für jeden virtuellen Computer. Dieser Wert findet sich in `VMComputer`.

In der folgenden Beispiel Abfrage wird eine Verknüpfung (join) verwendet, um diese Berechnung auszuführen. Der [distinct](./distinctoperator.md)-Operator wird mit `VMComputer` verwendet, weil Details regelmäßig von jedem Computer erfasst werden. Als Ergebnis werden mehrere Zeilen für jeden Computer in der Tabelle erstellt. Die beiden Tabellen werden mithilfe der Spalte `Computer` verknüpft. Im Resultset wird eine Zeile erstellt, die Spalten aus beiden Tabellen für jede Zeile in `InsightsMetrics` enthält, wobei ein Wert in `Computer` mit demselben Wert in der Spalte `Computer` in `VMComputer` übereinstimmt.

```kusto
VMComputer
| distinct Computer, PhysicalMemoryMB
| join kind=inner (
    InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val
) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

:::image type="content" source="images/tutorial/azure-monitor-join-results.png" lightbox="images/tutorial/azure-monitor-join-results.png" alt-text="Screenshot mit den Ergebnissen des join-Operatorbeispiels.":::

## <a name="assign-a-result-to-a-variable-let"></a>Zuweisen eines Ergebnisses zu einer Variablen: *let*

Verwenden Sie [let](./letstatement.md), um das Lesen und Verwalten von Abfragen zu vereinfachen. Sie können diesen Operator verwenden, um die Ergebnisse einer Abfrage einer Variablen zuzuweisen, die Sie später verwenden können. Mit der `let`-Anweisung kann die Abfrage im vorangehenden Beispiel wie folgt umgeschrieben werden:

 
```kusto
let PhysicalComputer = VMComputer
    | distinct Computer, PhysicalMemoryMB;
let AvailableMemory = InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val;
PhysicalComputer
| join kind=inner (AvailableMemory) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

:::image type="content" source="images/tutorial/azure-monitor-let-results.png" lightbox="images/tutorial/azure-monitor-let-results.png" alt-text="Screenshot mit den Ergebnissen des let-Operatorbeispiels.":::

## <a name="next-steps"></a>Nächste Schritte

- Anzeigen von Codebeispielen für die [Kusto-Abfragesprache](samples.md?pivots=azuremonitor).


::: zone-end
