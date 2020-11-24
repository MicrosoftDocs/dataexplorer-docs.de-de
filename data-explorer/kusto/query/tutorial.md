---
title: 'Tutorial: Kusto-Abfragen in Azure Daten-Explorer & Azure Monitor'
description: In diesem Tutorial wird beschrieben, wie Sie Abfragen in der Kusto-Abfragesprache verwenden, um allgemeine Abfrage Anforderungen in Azure Daten-Explorer und Azure Monitor zu erfüllen.
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
ms.openlocfilehash: 8a47c51aa7924a28b27602056ea869bfd7a09936
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783721"
---
# <a name="tutorial-use-kusto-queries-in-azure-data-explorer-and-azure-monitor"></a>Tutorial: Verwenden von Kusto-Abfragen in Azure Daten-Explorer und Azure Monitor

::: zone pivot="azuredataexplorer"

Die beste Möglichkeit, um mehr über die Kusto-Abfragesprache zu erfahren, besteht darin, einige grundlegende Abfragen zu untersuchen, um ein "Gefühl" für die Sprache zu erhalten. Es wird empfohlen, eine [Datenbank mit einigen Beispiel Daten zu](https://help.kusto.windows.net/Samples)verwenden. Die in diesem Tutorial gezeigten Abfragen sollten für diese Datenbank ausgeführt werden. Die- `StormEvents` Tabelle in der-Beispieldatenbank enthält Informationen zu stürmen, die im USA aufgetreten sind.

## <a name="count-rows"></a>Zeilen zählen

Die Beispieldatenbank enthält eine Tabelle mit dem Namen `StormEvents` . Um herauszufinden, wie groß die Tabelle ist, übergeben wir Ihren Inhalt an einen Operator, der einfach die Zeilen in der Tabelle zählt. 

*Syntax Hinweis*: eine Abfrage ist eine Datenquelle (normalerweise ein Tabellenname), gefolgt von einem oder mehreren Paaren des Senk Zeichens und einem tabellarischen Operator.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

So sieht die Ausgabe aus:

|Anzahl|
|-----|
|59066|
    
Weitere Informationen finden Sie unter [count-Operator](./countoperator.md).

## <a name="select-a-subset-of-columns-project"></a>Wählen Sie eine Teilmenge der Spalten aus: *Project* .

Verwenden Sie [Project](./projectoperator.md) , um nur die gewünschten Spalten auszuwählen. Sehen Sie sich das folgende Beispiel an, in dem sowohl [die-als auch die](./projectoperator.md) [Take](./takeoperator.md) -Operatoren verwendet werden

## <a name="filter-by-boolean-expression-where"></a>Nach booleschem Ausdruck filtern: *Where*

Sehen wir uns nur die `flood` Ereignisse in `California` Feb-2007 an:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

So sieht die Ausgabe aus:

|StartTime|EndTime|Staat|EventType|Episodenarrative|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|Kalifornische|Hochwasser|Ein frontaler System, das sich über den südlichen San-Joaquin Valley bewegt, hat in den frühen Morgenstunden des 19 Über Bundesland-Autobahn 166 in der Nähe von Taft wurde eine neben Überflutung|

## <a name="show-n-rows-take"></a>*N* Zeilen anzeigen: *übernehmen*

Sehen wir uns einige Daten an. Was ist eine zufällige Stichprobe von fünf Zeilen?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

So sieht die Ausgabe aus:

|StartTime|EndTime|EventType|Staat|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Starker Regen|Flori|Bis zu 9 Zoll in einem Zeitraum von 24 Stunden in Teilen des Küsten bauweiten Kreises.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Hüterin|Flori|Ein Tornado, der in der Stadt von Eustis am Nord Ende des West-kruklake liegt. Der Tornado hat sich schnell auf die EF1 Stärke verstärkt, als er Nord Nordwest durch Eustis verlagert hat. Die Nachverfolgung war direkt unter zwei km lang und hatte eine maximale Breite von 300 Meter.  Der Tornado hat 7 Häuser zerstört. 20 sieben Häuser erhielten größere Schäden, und 81 Häuser haben geringfügige Schäden gemeldet. Es sind keine schwerwiegenden Verletzungen aufgetreten, und der Eigenschafts Schaden wurde bei $6,2 Millionen festgelegt.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|Wasser Spout|Atlantik, Süden|Ein Wasserzeichen, das im Atlantik-Südosten des Melbourne-Strandes gebildet und kurz nach "Shore" verschoben wurde.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Sturm|Mississippi|Zahlreiche große Strukturen wurden mit einigen Netz Linien in der Luft gesprengt. In Ost Adams County ist ein Schaden aufgetreten.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Sturm|Georgien|Die Bezirks Verteilung hat berichtet, dass mehrere Strukturen entlang der Quincey Batch 10-Schleife in der Nähe von State Road 206 aufgesprengt wurden. Die Kosten der Baum Entfernung wurden geschätzt.|

Wenn [Sie jedoch](./takeoperator.md) Zeilen aus der Tabelle ohne bestimmte Reihenfolge anzeigen, Sortieren Sie Sie. ([Limit](./takeoperator.md) ist ein Alias für [Take](./takeoperator.md) und hat denselben Effekt.)

## <a name="order-results-sort-top"></a>Bestell Ergebnisse: *Sortieren*, nach *oben*

* *Syntax Hinweis*: einige Operatoren verfügen über Parameter, die von Schlüsselwörtern wie eingeführt werden `by` .
* Im folgenden Beispiel `desc` ergibt Orders eine absteigende Reihenfolge, und die Reihen `asc` Folge ergibt sich in aufsteigender Reihenfolge.

Die ersten *n* Zeilen anzeigen, geordnet nach einer bestimmten Spalte:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

So sieht die Ausgabe aus:

|StartTime|EndTime|EventType|Staat|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Winter Storm|MICHIGAN|Dieses schwere Schnee Ereignis wurde in den frühen Morgenstunden am Tag des neuen Jahrs fortgesetzt.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Winter Storm|MICHIGAN|Dieses schwere Schnee Ereignis wurde in den frühen Morgenstunden am Tag des neuen Jahrs fortgesetzt.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Winter Storm|MICHIGAN|Dieses schwere Schnee Ereignis wurde in den frühen Morgenstunden am Tag des neuen Jahrs fortgesetzt.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Hoher Wind|Kalifornische|In den Bergen von Ventura County wurden Nord-zu-Nordost-Windböen an ungefähr 58 mph gemeldet.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Hoher Wind|Kalifornische|Der Warm Springs RAWs-Sensor hat Nord Windböen gemeldet, die an 58 mph ausgegeben wurden.|

Sie können das gleiche Ergebnis mit [Sortieren](./sortoperator.md)erzielen [und dann Folgendes](./takeoperator.md)ausführen:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="compute-derived-columns-extend"></a>Abgeleitete Spalten berechnen: *erweitern*

Erstellen Sie eine neue Spalte, indem Sie in jeder Zeile einen Wert berechnen:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

So sieht die Ausgabe aus:

|StartTime|EndTime|Duration|EventType|Staat|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Starker Regen|Flori|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Hüterin|Flori|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|Wasser Spout|Atlantik, Süden|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Sturm|Mississippi|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Sturm|Georgien|

Es ist möglich, einen Spaltennamen wiederzuverwenden und der gleichen Spalte ein Berechnungs Ergebnis zuzuweisen.

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

[Skalare Ausdrücke](./scalar-data-types/index.md) können alle üblichen Operatoren ( `+` ,, `-` `*` , `/` ,) enthalten `%` , und es stehen verschiedene nützliche Funktionen zur Verfügung.

## <a name="aggregate-groups-of-rows-summarize"></a>Aggregat Gruppen von Zeilen: zusammen *fassen*

Zählen Sie die Anzahl der Ereignisse, die in jedem Land auftreten:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[fassen](./summarizeoperator.md) Sie Gruppen zusammen, die in der-Klausel dieselben Werte aufweisen `by` , und verwenden Sie dann eine Aggregations Funktion (z. b. `count` ), um die einzelnen Gruppen in einer einzelnen Zeile zu kombinieren. In diesem Fall gibt es eine Zeile für jeden Status und eine Spalte für die Anzahl der Zeilen in diesem Zustand.

Ein Bereich von [Aggregations Funktionen](./summarizeoperator.md#list-of-aggregation-functions) ist verfügbar. Sie können mehrere Aggregations Funktionen in einem `summarize` Operator verwenden, um mehrere berechnete Spalten zu entwickeln. Wir könnten beispielsweise die Anzahl der Stürme in jedem Zustand und auch eine Summe eines eindeutigen Typs von Stürmen pro Bundesstaat erhalten. Dann könnten wir [Top](./topoperator.md) verwenden, um die meisten Storm-betroffenen Zustände zu erhalten:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

So sieht die Ausgabe aus:

|Staat|Stormcount|Typeofstorms|
|---|---|---|
|TEXAS|4701|27|
|Kansas|3166|21|
|IOWA|2337|19|
|Illinois|2022|23|
|Missouri|2016|20|

In den Ergebnissen eines `summarize` Operators:

* Jede Spalte wird in benannt `by` .
* Jeder berechnete Ausdruck verfügt über eine-Spalte.
* Jede Kombination von `by` Werten weist eine Zeile auf.

## <a name="summarize-by-scalar-values"></a>Zusammenfassen nach Skalarwerten

Sie können skalare Werte (numeric, Time oder Interval) in der- `by` Klausel verwenden, aber Sie sollten die Werte mithilfe der [bin ()](./binfunction.md) -Funktion in Behälter platzieren:

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

[Bin ()](./binfunction.md) ist identisch mit der [Floor ()](./floorfunction.md) -Funktion in vielen Sprachen. Dadurch wird jeder Wert einfach auf das nächstgelegene Vielfache des von Ihnen angegebenen Modulo reduziert, sodass die Zeilen von zusammen [fassen](./summarizeoperator.md) den Gruppen zugewiesen werden können.

<a name="displaychartortable"></a>
## <a name="display-a-chart-or-table-render"></a>Anzeigen eines Diagramms oder einer Tabelle: *Rendering*

Sie können zwei Spalten projizieren und Sie als x-Achse und die y-Achse eines Diagramms verwenden:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="Screenshot, der ein Säulendiagramm der Anzahl von Storm-Ereignissen nach Bundesland anzeigt.":::

Obwohl wir den `mid` Vorgang entfernt `project` haben, benötigen wir ihn trotzdem, wenn wir möchten, dass das Diagramm die Länder in dieser Reihenfolge anzeigt.

Streng genommen `render` handelt es sich um eine Funktion des-Clients und nicht um einen Teil der-Abfragesprache. Dennoch ist es in die Sprache integriert, und es ist nützlich, um Ihre Ergebnisse zu entwerfen.


## <a name="timecharts"></a>Zeitdiagramme

Wenn wir zu numerischen Containern zurückkehren, zeigen wir eine Zeitreihe an:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="Screenshot eines Linien Diagramms von Ereignissen, die nach Zeit klassifiziert werden.":::

## <a name="multiple-series"></a>Mehrere Reihen

Verwenden Sie mehrere Werte in einer `summarize by` -Klausel, um eine separate Zeile für jede Kombination von Werten zu erstellen:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="Screenshot, der eine Tabellen Anzahl nach Quelle anzeigt.":::

Fügen Sie dem `render` vorangehenden Beispiel einfach den Begriff hinzu: `| render timechart` .

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="Screenshot, der eine Liniendiagramm Anzahl nach Quelle anzeigt.":::

Beachten Sie, dass `render timechart` die erste Spalte als x-Achse verwendet und die anderen Spalten dann als separate Zeilen anzeigt.

## <a name="daily-average-cycle"></a>Durchschnittlicher Tageszyklus

Wie unterscheidet sich die Aktivität über den durchschnittlichen Tag?

Zählen Sie Ereignisse nach Zeit Modulo einen Tag, klassifiziert in Stunden. Hier verwenden wir `floor` anstelle von `bin` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="Screenshot, der eine Zeitdiagramm Anzahl nach Stunde anzeigt.":::

Derzeit wird die `render` Dauer nicht ordnungsgemäß beschriftet, aber wir könnten `| render columnchart` stattdessen verwenden:

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="Screenshot, der eine Säulendiagramm Anzahl nach Stunde anzeigt.":::

## <a name="compare-multiple-daily-series"></a>Vergleichen mehrerer täglicher Reihen

Wie unterscheidet sich die Aktivität im Laufe der Tageszeit in verschiedenen Zuständen?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="Screenshot eines Zeit Diagramms nach Stunde und Zustand.":::

Dividieren `1h` Sie durch, um die x-Achse anstelle einer Dauer in eine Stundenzahl umzuwandeln:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="Screenshot, der ein Säulendiagramm nach Stunde und Zustand anzeigt":::

## <a name="join-data-types"></a>Joindatentypen

Wie würden Sie zwei spezifische Ereignis Typen finden und in welchem Zustand die einzelnen Ereignisse aufgetreten sind?

Sie können Storm-Ereignisse mit dem ersten `EventType` und dem zweiten abrufen `EventType` und dann die beiden Gruppen verknüpfen `State` :

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

:::image type="content" source="images/tutorial/join-events-lightning-avalanche.png" alt-text="Screenshot, der den Einstieg in die Ereignisse Blitz und Lawinen anzeigt":::

## <a name="user-session-example-of-join"></a>Benutzer Sitzungs Beispiel für *Join*

In diesem Abschnitt wird die-Tabelle nicht verwendet `StormEvents` .

Angenommen, Sie verfügen über Daten, die Ereignisse enthalten, die den Beginn und das Ende jeder Benutzersitzung mit einer eindeutigen ID für jede Sitzung markieren. 

Wie würden Sie feststellen, wie lange die einzelnen Benutzersitzungen dauern?

Sie können verwenden, `extend` um einen Alias für die beiden Zeitstempel anzugeben, und dann die Sitzungsdauer berechnen:

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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="Screenshot einer Tabelle mit den Ergebnissen für die Erweiterung der Benutzersitzung.":::

Es empfiehlt sich, `project` zu verwenden, um nur die Spalten auszuwählen, die Sie benötigen, bevor Sie den Join ausführen. Benennen Sie die Spalte in denselben Klauseln um `timestamp` .

## <a name="plot-a-distribution"></a>Darstellen einer Verteilung

`StormEvents`Wie viele Stürme haben unterschiedliche Längen, wenn Sie zur Tabelle zurückkehren?

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

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="Screenshot der Zeitdiagramm-Ergebnisse für die Ereignis Anzahl nach Dauer.":::

Sie können auch Folgendes verwenden `| render columnchart` :

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="Screenshot eines Säulen Diagramms für das Zeitdiagramm der Ereignis Anzahl nach Dauer.":::

## <a name="percentiles"></a>Perzentile

Welche Zeiträume werden in verschiedenen Prozentsätzen von Stürmen gefunden?

Um diese Informationen zu erhalten, verwenden Sie die vorherige Abfrage, aber ersetzen Sie `render` durch:

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

In diesem Fall haben wir keine- `by` Klausel verwendet, sodass die Ausgabe eine einzelne Zeile ist:

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" lightbox="images/tutorial/summarize-percentiles-duration.png" alt-text="Screenshot einer Tabelle mit Ergebnissen für das Zusammenfassen von Perzentilen nach Dauer.":::

Aus der Ausgabe wird Folgendes angezeigt:

* 5% der Stürme haben eine Dauer von weniger als 5 Minuten.
* 50% der Stürme dauerten weniger als eine Stunde und 25 Minuten.
* 5% der Stürme dauerten mindestens zwei Stunden und 50 Minuten.

Um eine separate Aufschlüsselung für jeden Zustand zu erhalten, verwenden Sie die `state` Spalte separat mit beiden `summarize` Operatoren:

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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="Tabelle fasst die Dauer des Perzentils nach Bundesstaat zusammen.":::

## <a name="assign-a-result-to-a-variable-let"></a>Zuweisen eines Ergebnisses zu einer Variablen: *Let*

Verwenden [Sie Let](./letstatement.md) , um die Teile des Abfrage Ausdrucks im vorangehenden Beispiel voneinander zu trennen `join` . Die Ergebnisse sind wie folgt unverändert:

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

In der folgenden Abfrage muss sich die `Logs` Tabelle in der Standarddatenbank befinden:

```kusto
Logs | where ...
```

Verwenden Sie die folgende Syntax, um auf eine Tabelle in einer anderen Datenbank zuzugreifen:

```kusto
database("db").Table
```

Wenn Sie z. b. über Daten `Diagnostics` Banken `Telemetry` mit dem Namen und verfügen und einige der Daten in den beiden Tabellen korrelieren möchten, können Sie die folgende Abfrage verwenden (vorausgesetzt, dass `Diagnostics` es sich um die Standarddatenbank handelt):

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

Verwenden Sie diese Abfrage, wenn die Standarddatenbank `Telemetry` :

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
Bei den vorherigen beiden Abfragen wird davon ausgegangen, dass sich beide Datenbanken im Cluster befinden, mit dem Sie gerade verbunden sind. Wenn sich die `Telemetry` Datenbank in einem Cluster mit dem Namen *TelemetryCluster.Kusto.Windows.net* befunden hat, verwenden Sie diese Abfrage, um darauf zuzugreifen:

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> Wenn der Cluster angegeben wird, ist die Datenbank obligatorisch.

Weitere Informationen zum Kombinieren von Daten aus mehreren Datenbanken in einer Abfrage finden Sie unter Daten [Bank übergreifende Abfragen](cross-cluster-or-database-queries.md).

## <a name="next-steps"></a>Nächste Schritte

- Anzeigen von Codebeispielen für die [Kusto-Abfragesprache](samples.md?pivots=azuredataexplorer).

::: zone-end

::: zone pivot="azuremonitor"

Die beste Möglichkeit, um mehr über die Kusto-Abfragesprache zu erfahren, besteht darin, einige grundlegende Abfragen zu untersuchen, um ein "Gefühl" für die Sprache zu erhalten. Diese Abfragen ähneln Abfragen, die im Azure Daten-Explorer-Tutorial verwendet werden, aber Sie verwenden stattdessen Daten aus gemeinsamen Tabellen in einem Azure Log Analytics-Arbeitsbereich. 

Führen Sie diese Abfragen mithilfe der Log Analytics im Azure-Portal aus. Log Analytics ist ein Tool, das Sie zum Schreiben von Protokoll Abfragen verwenden können. Verwenden Sie Protokolldaten in Azure Monitor, und Werten Sie dann die Ergebnisse der Protokoll Abfrage aus. Wenn Sie mit log Analytics nicht vertraut sind, führen Sie das [Log Analytics-Tutorial](/azure/azure-monitor/log-query/log-analytics-tutorial)aus.

Alle Abfragen in diesem Tutorial verwenden die [Log Analytics Demo Umgebung](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade). Sie können Ihre eigene Umgebung verwenden, aber Sie verfügen möglicherweise nicht über einige der hier verwendeten Tabellen. Da die Daten in der Demo Umgebung nicht statisch sind, können die Ergebnisse Ihrer Abfragen geringfügig von den hier gezeigten Ergebnissen abweichen.


## <a name="count-rows"></a>Zeilen zählen

Die Tabelle [insightmetrics](/azure/azure-monitor/reference/tables/insightsmetrics) enthält Leistungsdaten, die von Erkenntnissen wie Azure Monitor für VMS und Azure Monitor für Container gesammelt werden. Um herauszufinden, wie groß die Tabelle ist, übergeben wir Ihren Inhalt an einen Operator, der die Zeilen einfach zählt.

Eine Abfrage ist eine Datenquelle (normalerweise ein Tabellenname), gefolgt von einem oder mehreren Paaren des Senk Zeichens und einem tabellarischen Operator. In diesem Fall werden alle Datensätze aus der `InsightsMetrics` Tabelle zurückgegeben und dann an den [count-Operator](./countoperator.md)gesendet. Der- `count` Operator zeigt die Ergebnisse an, da der Operator der letzte Befehl in der Abfrage ist.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

So sieht die Ausgabe aus:

|Anzahl|
|-----|
|1.263.191|
    

## <a name="filter-by-boolean-expression-where"></a>Nach booleschem Ausdruck filtern: *Where*

Die [azureactivity](/azure/azure-monitor/reference/tables/azureactivity) -Tabelle enthält Einträge aus dem Azure-Aktivitätsprotokoll, das Einblicke in Ereignisse auf Abonnement-oder Verwaltungsebene bietet, die in Azure aufgetreten sind. Sehen wir uns nur die `Critical` Einträge in einer bestimmten Woche an.

Der [Where](/azure/data-explorer/kusto/query/whereoperator) -Operator ist in der Kusto-Abfragesprache üblich. `where` Filtert eine Tabelle mit Zeilen, die bestimmten Kriterien entsprechen. Im folgenden Beispiel werden mehrere-Befehle verwendet. Zuerst Ruft die Abfrage alle Datensätze für die Tabelle ab. Anschließend werden die Daten nur für Datensätze im Zeitbereich gefiltert. Schließlich filtert Sie diese Ergebnisse nur für Datensätze, die über eine `Critical` Ebene verfügen.

> [!NOTE]
> Zusätzlich zur Angabe eines Filters in der Abfrage mithilfe der- `TimeGenerated` Spalte können Sie den Zeitbereich in Log Analytics angeben. Weitere Informationen finden Sie unter [Protokoll Abfrage Bereich und Zeitbereich in Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope).

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

:::image type="content" source="images/tutorial/azure-monitor-where-results.png" lightbox="images/tutorial/azure-monitor-where-results.png" alt-text="Screenshot, der die Ergebnisse des Beispiels für den Where-Operator anzeigt":::

## <a name="select-a-subset-of-columns-project"></a>Wählen Sie eine Teilmenge der Spalten aus: *Project* .

Verwenden Sie [Project](./projectoperator.md) , um nur die gewünschten Spalten einzuschließen. Im vorherigen Beispiel können wir die Ausgabe auf bestimmte Spalten beschränken:

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

:::image type="content" source="images/tutorial/azure-monitor-project-results.png" lightbox="images/tutorial/azure-monitor-project-results.png" alt-text="Screenshot, der die Ergebnisse des Beispiel für den Projekt Operator anzeigt.":::

## <a name="show-n-rows-take"></a>*N* Zeilen anzeigen: *übernehmen*

[Network Monitoring](/azure/azure-monitor/reference/tables/networkmonitoring) enthält Überwachungsdaten für virtuelle Azure-Netzwerke. Verwenden Sie den [Take](./takeoperator.md) -Operator, um fünf zufällige Beispiel Zeilen in dieser Tabelle zu betrachten. Der [Vorgang](./takeoperator.md) zeigt eine bestimmte Anzahl von Zeilen aus einer Tabelle ohne bestimmte Reihenfolge an:

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-take-results.png" lightbox="images/tutorial/azure-monitor-take-results.png" alt-text="Screenshot, der die Ergebnisse des Beispiels &quot;Take Operator&quot; anzeigt.":::

## <a name="order-results-sort-top"></a>Bestell Ergebnisse: *Sortieren*, nach *oben*

Anstelle zufälliger Datensätze können wir die letzten fünf Datensätze zurückgeben, indem wir zuerst nach Zeit sortieren:

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

Sie können dieses exakte Verhalten erzielen, indem Sie stattdessen den [Top](./topoperator.md) -Operator verwenden: 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-top-results.png" lightbox="images/tutorial/azure-monitor-top-results.png" alt-text="Screenshot, der die Ergebnisse des Top-Operator Beispiels anzeigt.":::

## <a name="compute-derived-columns-extend"></a>Abgeleitete Spalten berechnen: *erweitern*

Der Operator " [erweitern](./projectoperator.md) " ähnelt " [Project](./projectoperator.md)", aber er fügt dem Satz von Spalten hinzu, anstatt ihn zu ersetzen. Sie können beide Operatoren verwenden, um eine neue Spalte auf Grundlage einer Berechnung für jede Zeile zu erstellen.

Die [perf](/azure/azure-monitor/reference/tables/perf) -Tabelle enthält Leistungsdaten, die von virtuellen Computern gesammelt werden, auf denen der Log Analytics-Agent ausgeführt wird. 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

:::image type="content" source="images/tutorial/azure-monitor-extend-results.png" lightbox="images/tutorial/azure-monitor-extend-results.png" alt-text="Screenshot, der die Ergebnisse des Beispiels zum Erweitern eines Operators anzeigt.":::

## <a name="aggregate-groups-of-rows-summarize"></a>Aggregat Gruppen von Zeilen: zusammen *fassen*

Der [Zusammenfassungs Operator gruppiert](./summarizeoperator.md) Zeilen, die die gleichen Werte in der- `by` Klausel aufweisen. Anschließend wird eine Aggregations Funktion wie verwendet `count` , um die einzelnen Gruppen in einer einzelnen Zeile zu kombinieren. Ein Bereich von [Aggregations Funktionen](./summarizeoperator.md#list-of-aggregation-functions) ist verfügbar. Sie können mehrere Aggregations Funktionen in einem `summarize` Operator verwenden, um mehrere berechnete Spalten zu entwickeln. 

Die Tabelle " [securityevent](/azure/azure-monitor/reference/tables/securityevent) " enthält Sicherheitsereignisse wie Anmeldungen und Prozesse, die auf überwachten Computern gestartet wurden. Sie können zählen, wie viele Ereignisse jeder Ebene auf den einzelnen Computern aufgetreten sind. In diesem Beispiel wird eine Zeile für jede Kombination aus Computer und Ebene erstellt. Eine Spalte enthält die Anzahl der Ereignisse.

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-count-results.png" lightbox="images/tutorial/azure-monitor-summarize-count-results.png" alt-text="Screenshot, der die Ergebnisse des Beispiels zum Zusammenfassen des Count-Operators anzeigt.":::

## <a name="summarize-by-scalar-values"></a>Zusammenfassen nach Skalarwerten

Sie können durch skalare Werte wie Zahlen und Uhrzeitwerte aggregieren, aber Sie sollten die [bin ()](./binfunction.md) -Funktion verwenden, um Zeilen in unterschiedliche Datensätze zu gruppieren. Wenn Sie z. b. mit aggregieren `TimeGenerated` , erhalten Sie eine Zeile für fast jeden Uhrzeitwert. Verwenden `bin()` Sie, um diese Werte in Stunde oder Tag zu konsolidieren.

Die Tabelle [insightmetrics](/azure/azure-monitor/reference/tables/insightsmetrics) enthält Leistungsdaten, die von Erkenntnissen wie Azure Monitor für VMS und Azure Monitor für Container gesammelt werden. Die folgende Abfrage zeigt die stündliche durchschnittliche Prozessorauslastung für mehrere Computer:

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-avg-results.png" lightbox="images/tutorial/azure-monitor-summarize-avg-results.png" alt-text="Screenshot, der die Ergebnisse des AVG-Operator Beispiels anzeigt.":::

## <a name="display-a-chart-or-table-render"></a>Anzeigen eines Diagramms oder einer Tabelle: *Rendering*

Der [renderoperator](./renderoperator.md?pivots=azuremonitor) gibt an, wie die Ausgabe der Abfrage gerendert wird. Log Analytics rendert die Ausgabe standardmäßig als Tabelle. Nachdem Sie die Abfrage ausgeführt haben, können Sie verschiedene Diagrammtypen auswählen. Der- `render` Operator ist nützlich, wenn Abfragen in Abfragen eingeschlossen werden, bei denen ein bestimmter Diagrammtyp normalerweise bevorzugt wird.

Das folgende Beispiel zeigt die stündliche durchschnittliche Prozessorauslastung für einen einzelnen Computer. Die Ausgabe wird als Zeitdiagramm gerendert.

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-results.png" lightbox="images/tutorial/azure-monitor-render-results.png" alt-text="Screenshot, der die Ergebnisse des Beispiels &quot;Rendering-Operator&quot; anzeigt.":::

## <a name="work-with-multiple-series"></a>Arbeiten mit mehreren Reihen

Wenn Sie mehrere Werte in einer- `summarize by` Klausel verwenden, wird im Diagramm eine separate Reihe für jeden Satz von Werten angezeigt:

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-multiple-results.png" lightbox="images/tutorial/azure-monitor-render-multiple-results.png" alt-text="Screenshot, der die Ergebnisse des Beispiels &quot;Rendering-Operator mit mehreren Reihen&quot; anzeigt.":::

## <a name="join-data-from-two-tables"></a>Verknüpfen von Daten aus zwei Tabellen

Was geschieht, wenn Sie Daten aus zwei Tabellen in einer einzelnen Abfrage abrufen müssen? Sie können den [Join](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) -Operator verwenden, um Zeilen aus mehreren Tabellen in einem einzelnen Resultset zu kombinieren. Jede Tabelle muss eine Spalte mit einem übereinstimmenden Wert aufweisen, damit der Join weiß, welche Zeilen übereinstimmen.

[Vmcomputer](/azure/azure-monitor/reference/tables/vmcomputer) ist eine Tabelle, die von Azure Monitor für VMS verwendet wird, um Details zu den von ihm überwachten virtuellen Maschinen zu speichern. [Insighungmetrics](/azure/azure-monitor/reference/tables/insightsmetrics) enthält Leistungsdaten, die von diesen virtuellen Computern gesammelt werden. Ein in *insightionmetrics* erfasster Wert ist der verfügbare Arbeitsspeicher, jedoch nicht der verfügbare Arbeitsspeicher in Prozent. Um den Prozentsatz zu berechnen, benötigen wir den physischen Speicher für jeden virtuellen Computer. Dieser Wert befindet sich in `VMComputer` .

In der folgenden Beispiel Abfrage wird ein Join verwendet, um diese Berechnung auszuführen. Der unter [schiedliche](/azure/data-explorer/kusto/query/distinctoperator) Operator wird mit verwendet, `VMComputer` da Details regelmäßig von jedem Computer gesammelt werden. Als Ergebnis werden mehrere Zeilen für jeden Computer in der Tabelle erstellt. Die beiden Tabellen werden mithilfe der- `Computer` Spalte verknüpft. Im Resultset wird eine Zeile mit Spalten aus beiden Tabellen für jede Zeile in erstellt `InsightsMetrics` , wobei ein Wert in mit `Computer` dem gleichen Wert in der `Computer` Spalte in übereinstimmt `VMComputer` .

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

:::image type="content" source="images/tutorial/azure-monitor-join-results.png" lightbox="images/tutorial/azure-monitor-join-results.png" alt-text="Screenshot, der die Ergebnisse des Join-Operators-Beispiels anzeigt":::

## <a name="assign-a-result-to-a-variable-let"></a>Zuweisen eines Ergebnisses zu einer Variablen: *Let*

Verwenden [Sie Let](./letstatement.md) , um das Lesen und Verwalten von Abfragen zu vereinfachen. Sie können diesen Operator verwenden, um die Ergebnisse einer Abfrage einer Variablen zuzuweisen, die Sie später verwenden können. Mithilfe der- `let` Anweisung kann die Abfrage im vorangehenden Beispiel wie folgt umgeschrieben werden:

 
```kusto
let PhysicalComputer = VMComputer
    | distinct Computer, PhysicalMemoryMB;
    let AvailableMemory = 
InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val;
PhysicalComputer
| join kind=inner (AvailableMemory) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

:::image type="content" source="images/tutorial/azure-monitor-let-results.png" lightbox="images/tutorial/azure-monitor-let-results.png" alt-text="Screenshot, der die Ergebnisse des Let-Operator Beispiels anzeigt.":::

## <a name="next-steps"></a>Nächste Schritte

- Anzeigen von Codebeispielen für die [Kusto-Abfragesprache](samples.md?pivots=azuremonitor).


::: zone-end
