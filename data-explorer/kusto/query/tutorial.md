---
title: 'Tutorial: Azure Daten-Explorer'
description: Dieser Artikel beschreibt das Tutorial in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c87979b788c83a819f9e65658b5e44e40e53ad1d
ms.sourcegitcommit: 0820454feb02ae489f3a86b688690422ae29d788
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94932734"
---
# <a name="tutorial"></a>Lernprogramm

::: zone pivot="azuredataexplorer"

Die beste Möglichkeit, um mehr über die Kusto-Abfragesprache zu erfahren, besteht darin, einige einfache Abfragen zu untersuchen, um das "Gefühl" für die Sprache zu erhalten, indem Sie eine [Datenbank mit Beispiel Daten](https://help.kusto.windows.net/Samples)verwenden. Die in diesem Artikel gezeigten Abfragen sollten für diese Datenbank ausgeführt werden. Die `StormEvents` Tabelle in dieser Beispieldatenbank enthält Informationen zu den in den USA

<!--
  TODO: Provide link to reference data we used originally in StormEvents
-->

<!--
  TODO: A few samples below reference non-existent tables, such as Events and Logs.
        We need to add these tables.
-->

## <a name="count-rows"></a>Zeilen zählen

Die Beispieldatenbank enthält eine Tabelle mit dem Namen `StormEvents` .
Um herauszufinden, wie groß es ist, übergeben wir den Inhalt an einen Operator, der die Zeilen einfach zählt:

* *Syntax:* Eine Abfrage ist eine Datenquelle (normalerweise ein Tabellenname), gefolgt von einem oder mehreren Paaren des Senk Zeichens und einem tabellarischen Operator.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

Das Ergebnis lautet wie folgt:

|Anzahl|
|-----|
|59066|
    
[count-Operator](./countoperator.md).

## <a name="project-select-a-subset-of-columns"></a>Projekt: Wählen Sie eine Teilmenge von Spalten aus.

Verwenden Sie [Project](./projectoperator.md) , um nur die gewünschten Spalten auszuwählen. Siehe Beispiel unten, das sowohl [Project](./projectoperator.md) als auch [Take](./takeoperator.md) Operator verwendet.

## <a name="where-filtering-by-a-boolean-expression"></a>WHERE: Filtern nach einem booleschen Ausdruck

Sehen wir uns `flood` im `California` Februar-2007 nur die e an:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

|StartTime|EndTime|State|EventType|Episodenarrative|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|Kalifornische|Hochwasser|Ein frontaler System, das sich über den südlichen San-Joaquin Valley bewegt, hat in den frühen Morgenstunden des 19 Über Bundesland-Autobahn 166 in der Nähe von Taft wurde eine neben Überflutung|

## <a name="take-show-me-n-rows"></a>Take: Anzeigen von n Zeilen

Betrachten Sie einige Daten: Was sehen Sie in 5 Beispielzeilen?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|State|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Starker Regen|Flori|Bis zu 9 Zoll in einem Zeitraum von 24 Stunden in Teilen des Küsten bauweiten Kreises.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Hüterin|Flori|Ein Tornado, der in der Stadt von Eustis am Nord Ende des West-kruklake liegt. Der Tornado hat sich schnell auf die EF1 Stärke verstärkt, als er Nord Nordwest durch Eustis verlagert hat. Die Nachverfolgung war direkt unter zwei km lang und hatte eine maximale Breite von 300 Meter.  Der Tornado hat 7 Häuser zerstört. 20 sieben Häuser erhielten größere Schäden, und 81 Häuser haben geringfügige Schäden gemeldet. Es sind keine schwerwiegenden Verletzungen aufgetreten, und der Eigenschafts Schaden wurde bei $6,2 Millionen festgelegt.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|Wasser Spout|Atlantik, Süden|Ein Wasserzeichen, das im Atlantik-Südosten des Melbourne-Strandes gebildet und kurz nach "Shore" verschoben wurde.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Sturm|Mississippi|Zahlreiche große Strukturen wurden mit einigen Netz Linien in der Luft gesprengt. In Ost Adams County ist ein Schaden aufgetreten.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Sturm|Georgien|Die Bezirks Verteilung hat berichtet, dass mehrere Strukturen entlang der Quincey Batch 10-Schleife in der Nähe von State Road 206 aufgesprengt wurden. Die Kosten der Baum Entfernung wurden geschätzt.|

Wenn [Sie jedoch](./takeoperator.md) Zeilen aus der Tabelle ohne bestimmte Reihenfolge anzeigen, Sortieren Sie Sie.
* " [Limit](./takeoperator.md) " ist ein Alias für " [Take](./takeoperator.md) " und hat die gleiche Wirkung.

## <a name="sort-and-top"></a>Sortieren und nach oben

* *Syntax:* Einige Operatoren verfügen über Parameter, die von Schlüsselwörtern wie eingeführt werden `by` .
* `desc` = absteigende Reihenfolge, `asc` = aufsteigende Reihenfolge.

Zeigen Sie die ersten n Zeilen, sortiert nach einer bestimmten Spalte an:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|State|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Winter Storm|MICHIGAN|Dieses schwere Schnee Ereignis wurde in den frühen Morgenstunden am Tag des neuen Jahrs fortgesetzt.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Winter Storm|MICHIGAN|Dieses schwere Schnee Ereignis wurde in den frühen Morgenstunden am Tag des neuen Jahrs fortgesetzt.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Winter Storm|MICHIGAN|Dieses schwere Schnee Ereignis wurde in den frühen Morgenstunden am Tag des neuen Jahrs fortgesetzt.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Hoher Wind|Kalifornische|In den Bergen von Ventura County wurden Nord-zu-Nordost-Windböen an ungefähr 58 mph gemeldet.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Hoher Wind|Kalifornische|Der Warm Springs RAWs-Sensor hat Nord Windböen gemeldet, die an 58 mph ausgegeben wurden.|

Dies kann mithilfe des [Sortier](./sortoperator.md) -und anschließenden [Operators erreicht](./takeoperator.md) werden.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="extend-compute-derived-columns"></a>erweitern: abgeleitete Spalten berechnen

Erstellen Sie eine neue Spalte, indem Sie in jeder Zeile einen Wert berechnen:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

|StartTime|EndTime|Duration|EventType|State|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Starker Regen|Flori|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Hüterin|Flori|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|Wasser Spout|Atlantik, Süden|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Sturm|Mississippi|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Sturm|Georgien|

Es ist möglich, den Spaltennamen wiederzuverwenden und das Berechnungs Ergebnis der gleichen Spalte zuzuweisen.
Beispiel:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

|x|y|
|---|---|
|3|1|

[Skalare Ausdrücke](./scalar-data-types/index.md) können alle üblichen Operatoren ( `+` , `-` , `*` , `/` ,) enthalten `%` , und es gibt eine Reihe nützlicher Funktionen.

## <a name="summarize-aggregate-groups-of-rows"></a>Zusammenfassung: Aggregieren von Zeilen Gruppen

Zählen der Anzahl von Ereignissen aus jedem Land:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[fassen](./summarizeoperator.md) Sie Gruppen zusammen, die in der-Klausel dieselben Werte aufweisen `by` , und verwenden Sie dann die Aggregations Funktion (z `count` . b.), um die einzelnen Gruppen in einer einzelnen Zeile zu kombinieren. In diesem Fall gibt es eine Zeile für jeden Status und eine Spalte für die Anzahl der Zeilen in diesem Zustand.

Es gibt eine Reihe von [Aggregations Funktionen](./summarizeoperator.md#list-of-aggregation-functions), die Sie in einem Zusammenfassungs Operator zum entwickeln mehrerer berechneter Spalten verwenden können. Wir könnten beispielsweise die Anzahl der Stürme in jedem Status und auch eine Summe der eindeutigen Art von Stürmen pro Bundesstaat erhalten.  
dann könnten wir [Top](./topoperator.md) verwenden, um die meisten Storm-betroffenen Zustände zu erhalten:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

|State|Stormcount|Typeofstorms|
|---|---|---|
|TEXAS|4701|27|
|Kansas|3166|21|
|IOWA|2337|19|
|Illinois|2022|23|
|Missouri|2016|20|

Das Ergebnis von „summarize“ verfügt über Folgendes:

* jede in `by`genannte Spalte
* eine Spalte für jeden berechneten Ausdruck.
* eine Zeile für jede Kombination von `by` -Werten

## <a name="summarize-by-scalar-values"></a>Zusammenfassen nach Skalarwerten

Sie können Skalarwerte (numerisch, Zeit oder Intervall) in der- `by` Klausel verwenden, aber Sie sollten die Werte in Behälter platzieren.  
Hierfür ist die [bin ()](./binfunction.md) -Funktion nützlich:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

Dadurch werden alle Zeitstempel auf Intervalle von einem Tag reduziert:

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

## <a name="render-display-a-chart-or-table"></a>Rendering: Diagramm oder Tabelle anzeigen

Projizieren Sie zwei Spalten, und verwenden Sie Sie als x-und y-Achse eines Diagramms:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="Säulendiagramm der Anzahl von Storm-Ereignissen nach Status":::

Obwohl wir den `mid` Projekt Vorgang entfernt haben, benötigen wir ihn trotzdem, wenn wir möchten, dass das Diagramm die Länder in dieser Reihenfolge anzeigt.

Streng genommen handelt es sich bei "Rendering" nicht um einen Teil der Abfragesprache, sondern um eine Funktion des-Clients. Dennoch ist es in die Sprache integriert und ist sehr nützlich, um Ihre Ergebnisse zu entwerfen.


## <a name="timecharts"></a>Zeitdiagramme

Wenn wir zu numerischen Containern zurückkehren, zeigen wir eine Zeitreihe an:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="Zeilendiagramm Ereignisse, klassifiziert nach Zeit":::

## <a name="multiple-series"></a>Mehrere Reihen

Verwenden Sie mehrere Werte in einer `summarize by` -Klausel, um eine separate Zeile für jede Kombination von Werten zu erstellen:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="Tabellen Anzahl nach Quelle":::

Fügen Sie einfach den "Rendering"-Begriff zum obigen hinzu: `| render timechart` .

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="Liniendiagramm Anzahl nach Quelle":::

Beachten Sie, dass `render timechart` die erste Spalte als x-Achse verwendet und die anderen Spalten dann als separate Zeilen anzeigt.

## <a name="daily-average-cycle"></a>Durchschnittlicher Tageszyklus

Wie unterscheidet sich die Aktivität über den durchschnittlichen Tag?

Zählen Sie Ereignisse nach Zeit Modulo einen Tag, klassifiziert in Stunden. Beachten Sie, dass `floor` anstelle von "bin" verwendet wird:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="Zeitdiagramm Anzahl nach Stunde":::

Derzeit wird die `render` Dauer nicht ordnungsgemäß beschriftet, aber wir könnten `| render columnchart` stattdessen verwenden:

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="Säulendiagramm Anzahl nach Stunde":::

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

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="Zeitdiagramm nach Stunde und Zustand":::

Dividieren `1h` Sie durch, um die x-Achse in Stundenzahl anstelle einer Dauer zu verwandeln:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="Säulendiagramm nach Stunde und Zustand":::

## <a name="join"></a>Join

Wie finde ich zwei angegebene EventTypes, in welchem Zustand beide aufgetreten sind?

Sie können Storm-Ereignisse mit dem ersten EventType und mit dem zweiten EventType abrufen und dann die beiden Sätze für den Zustand verknüpfen.

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

:::image type="content" source="images/tutorial/join-events-la.png" alt-text="Ereignisse Blitz und Lawinen in Verbindung treten":::

## <a name="user-session-example-of-join"></a>Benutzer Sitzungs Beispiel für Join

In diesem Abschnitt wird die- `StormEvents` Tabelle nicht verwendet.

Angenommen, Sie verfügen über Daten, die Ereignisse enthalten, die den Beginn und das Ende jeder Benutzersitzung markieren, mit einer eindeutigen ID für jede Sitzung. 

Wie lange dauert jede Benutzersitzung?

Wenn `extend` Sie verwenden, um einen Alias für die beiden Zeitstempel anzugeben, können Sie die Sitzungsdauer berechnen.

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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="Erweiterung für Benutzersitzung":::

Es ist üblich, `project` zu verwenden, um vor dem Verknüpfen nur die Spalten auszuwählen, die wir benötigen.
In den gleichen Klauseln benennen wir die Zeitstempelspalte um.

## <a name="plot-a-distribution"></a>Darstellen einer Verteilung

Wie viele Stürme gibt es unterschiedliche Längen?

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

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="Ereignis Anzahl Zeitdiagramm nach Dauer":::

Oder verwenden Sie Folgendes `| render columnchart` :

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="Säulendiagramm Ereignis Anzahl Zeitdiagramm nach Dauer":::

## <a name="percentiles"></a>Perzentile

Welche Zeiträume von dauern decken verschiedene Prozentsätze von Stürmen ab?

Verwenden Sie die obige Abfrage, aber ersetzen Sie `render` durch:

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

In diesem Fall haben wir keine- `by` Klausel bereitgestellt, sodass das Ergebnis eine einzelne Zeile ist:

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" alt-text="Tabelle fasst Perzentilen nach Dauer zusammen":::

Daraus lässt sich Folgendes ablesen:

* 5% der Stürme haben eine Dauer von weniger als 5 m.
* 50% der Stürme sind kleiner als 1 h 25 m.
* 5% der Stürme sind mindestens 2 Std. 50 ms.

Um eine separate Aufschlüsselung für jeden Zustand zu erhalten, müssen wir die Spalte State separat über beide Zusammenfassungs Operatoren hinzu bringen:

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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="Tabelle fasst die Dauer des Perzentils nach Status zusammen.":::


## <a name="let-assign-a-result-to-a-variable"></a>let: Zuweisen eines Ergebnisses zu einer Variablen

Verwenden [Sie Let](./letstatement.md) , um die Teile des Abfrage Ausdrucks im obigen Beispiel "Join" zu trennen. Die Ergebnisse sind wie folgt unverändert:

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
> Fügen Sie im Kusto-Explorer-Client keine leeren Zeilen zwischen den Teilen dieser ein. Stellen Sie sicher, dass Sie alles ausführen.

## <a name="combining-data-from-several-databases-in-a-query"></a>Kombinieren von Daten aus mehreren Datenbanken in einer Abfrage

Ausführliche Erörterung finden Sie unter [datenbankübergreifende Abfragen](./cross-cluster-or-database-queries.md)

Wenn Sie eine Abfrage des Stils schreiben:

```kusto
Logs | where ...
```

Die Tabelle mit dem Namen Logs muss sich in ihrer Standarddatenbank befinden. Wenn Sie auf Tabellen aus einer anderen Datenbank zugreifen möchten, verwenden Sie die folgende Syntax:

```kusto
database("db").Table
```

Wenn Sie also über Datenbanken mit dem Namen " *Diagnostics* " und " *Telemetrie* " verfügen und einige Ihrer Daten korrelieren möchten, können Sie schreiben (angenommen, die *Diagnose* ist die Standarddatenbank).

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

oder wenn die Standarddatenbank *Telemetrie* ist

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
Im obigen Beispiel wird davon ausgegangen, dass sich beide Datenbanken in dem Cluster befinden, mit dem Sie derzeit verbunden sind. Angenommen, die *telemetriedatenbank* gehörte zu einem anderen Cluster mit dem Namen *TelemetryCluster.Kusto.Windows.net* , um darauf zugreifen zu können.

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> Wenn der Cluster angegeben wird, ist die Datenbank obligatorisch.




::: zone-end

::: zone pivot="azuremonitor"

Die beste Möglichkeit, um mehr über die Kusto-Abfragesprache zu erfahren, besteht darin, einige einfache Abfragen anzusehen, um das "Gefühl" für die Sprache zu erhalten. Diese Abfragen ähneln denen im Tutorial zu Azure-Daten-Explorer, Sie verwenden jedoch Daten aus gemeinsamen Tabellen in einem Log Analytics Arbeitsbereich. 

Führen Sie diese Abfragen mit log Analytics aus. Hierbei handelt es sich um ein Tool im Azure-Portal zum Schreiben von Protokoll Abfragen mithilfe von Protokolldaten in Azure Monitor und zum Auswerten der Ergebnisse. Wenn Sie mit log Analytics nicht vertraut sind, können Sie ein Tutorial in [Log Analytics Tutorial](/azure/azure-monitor/log-query/log-analytics-tutorial)durchlaufen.

Alle Abfragen hier verwenden die [Log Analytics Demo Umgebung](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade). Sie können Ihre eigene Umgebung verwenden, aber Sie verfügen möglicherweise nicht über einige der hier verwendeten Tabellen. Da die Daten in der Demo Umgebung nicht statisch sind, können die Ergebnisse Ihrer Abfragen geringfügig von den hier gezeigten Ergebnissen abweichen.


## <a name="count-rows"></a>Zeilen zählen
[Insightmetrics](/azure/azure-monitor/reference/tables/insightsmetrics) enthält Leistungsdaten, die durch Einblicke gesammelt werden, z. b. Azure Monitor für VMS und Azure Monitor für Container. Um herauszufinden, wie groß es ist, übergeben wir den Inhalt an einen Operator, der die Zeilen einfach zählt:

Eine Abfrage ist eine Datenquelle (normalerweise ein Tabellenname), gefolgt von einem oder mehreren Paaren des Senk Zeichens und einem tabellarischen Operator. In diesem Fall werden alle Datensätze aus der insightzmetrics-Tabelle zurückgegeben und dann an den Operator [count Operator](./countoperator.md) gesendet, der die Ergebnisse ausgibt, weil es sich um den letzten Befehl in der Abfrage handelt.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

Das Ergebnis lautet wie folgt:

|Anzahl|
|-----|
|1.263.191|
    



## <a name="where-filtering-by-a-boolean-expression"></a>WHERE: Filtern nach einem booleschen Ausdruck
[Azureactivity](/azure/azure-monitor/reference/tables/azureactivity) enthält Einträge aus dem Azure-Aktivitätsprotokoll, das Einblicke in Ereignisse auf Abonnement Ebene oder auf Verwaltungs Gruppenebene bietet, die in Azure aufgetreten sind. Sehen wir uns nur die `Critical` Einträge in einer bestimmten Woche an.


Der [Where](/azure/data-explorer/kusto/query/whereoperator) -Operator ist in kql sehr häufig und filtert eine Tabelle mit Zeilen, die den angegebenen Kriterien entsprechen. In diesem Beispiel werden mehrere-Befehle verwendet. Die Abfrage ruft zuerst alle Datensätze für die Tabelle ab, filtert diese Daten dann nur für die Datensätze im Zeitbereich und filtert diese Ergebnisse nur nach Datensätzen mit einer `Critical` Ebene.

> [!NOTE]
> Zusätzlich zur Angabe eines Filters in der Abfrage mithilfe der- `TimeGenerated` Spalte können Sie den Zeitbereich in Log Analytics angeben. Ausführliche Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope).

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

[![Ergebnisse von WHERE Filter example](images/tutorial/am-results-where.png)](images/tutorial/am-results-where.png#lightbox)


## <a name="project-select-a-subset-of-columns"></a>Projekt: Wählen Sie eine Teilmenge von Spalten aus.

Verwenden Sie [Project](./projectoperator.md) , um nur die gewünschten Spalten auszuwählen. Im vorherigen Beispiel können wir die Ausgabe auf bestimmte Spalten beschränken.

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

[![Ergebnisse des Projekts (Beispiel)](images/tutorial/am-results-project.png)](images/tutorial/am-results-project.png#lightbox)


## <a name="take-show-me-n-rows"></a>Take: Anzeigen von n Zeilen
[Network Monitoring](/azure/azure-monitor/reference/tables/networkmonitoring) verfügt über Überwachungsdaten für virtuelle Azure-Netzwerke. Verwenden Sie den [Take](./takeoperator.md) -Operator, um fünf Beispiel Zeilen in der Tabelle zu sehen. Der [Vorgang](./takeoperator.md) zeigt eine bestimmte Anzahl von Zeilen aus einer Tabelle in keiner bestimmten Reihenfolge an.

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

[![Ergebnisse von Take example](images/tutorial/am-results-take.png)](images/tutorial/am-results-take.png#lightbox)

## <a name="sort-and-top"></a>Sortieren und nach oben
Anstelle zufälliger Datensätze können wir die letzten 5 Datensätze zurückgeben, indem wir zuerst nach Zeit sortieren.

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

Sie können dieses exakte Verhalten erzielen, indem Sie stattdessen den [Top](./topoperator.md) -Operator verwenden. 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

[![Ergebnisse des Top-Beispiels](images/tutorial/am-results-top.png)](images/tutorial/am-results-top.png#lightbox)


## <a name="extend-compute-derived-columns"></a>erweitern: abgeleitete Spalten berechnen
Der [erweiterungsoperator](./projectoperator.md) ähnelt [Project](./projectoperator.md) , außer dass er dem Satz von Spalten hinzufügt, anstatt ihn zu ersetzen. Sie können auch beide Operatoren verwenden, um eine neue Spalte auf Grundlage einer Berechnung für jede Zeile zu erstellen.

In der [perf](/azure/azure-monitor/reference/tables/perf) -Tabelle werden Leistungsdaten von virtuellen Computern gesammelt, auf denen der Log Analytics-Agent ausgeführt wird. 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

[![Ergebnisse der Erweiterungs Beispiele](images/tutorial/am-results-extend.png)](images/tutorial/am-results-extend.png#lightbox)


## <a name="summarize-aggregate-groups-of-rows"></a>Zusammenfassung: Aggregieren von Zeilen Gruppen
Der Zusammenfassungs Operator gruppiert Zeilen [, die die](./summarizeoperator.md) gleichen Werte in der `by` -Klausel aufweisen, und verwendet dann eine Aggregations Funktion, wie z `count` . b., um jede Gruppe in einer einzelnen Zeile zu kombinieren. Es gibt eine Reihe von [Aggregations Funktionen](./summarizeoperator.md#list-of-aggregation-functions), die Sie in einem Zusammenfassungs Operator zum entwickeln mehrerer berechneter Spalten verwenden können. 

Das [securityevent](/azure/azure-monitor/reference/tables/securityevent) -Ereignis enthält Sicherheitsereignisse, z. b. Anmeldungen und Prozesse, die auf überwachten Computern starten. Wir können zählen, wie viele Ereignisse jeder Ebene auf den einzelnen Computern aufgetreten sind. In diesem Beispiel gibt es eine Zeile für jede Kombination aus Computer und Ebene und eine Spalte für die Anzahl von Ereignissen.

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

[![Beispiel für Ergebnisse der Zusammenfassungs Anzahl](images/tutorial/am-results-summarize-count.png)](images/tutorial/am-results-summarize-count.png#lightbox)


## <a name="summarize-by-scalar-values"></a>Zusammenfassen nach Skalarwerten
Sie können durch skalare Werte (z. b. Zahlen und Uhrzeitwerte) aggregieren, aber Sie sollten die [bin ()](./binfunction.md) -Funktion verwenden, um Zeilen in unterschiedliche Datensätze zu gruppieren. Wenn Sie z. b. mit aggregieren `TimeGenerated` , erhalten Sie eine Zeile für fast jeden Uhrzeitwert. `bin()` um diese Werte in Stunde oder Tag zu konsolidieren.

[Insightmetrics](/azure/azure-monitor/reference/tables/insightsmetrics) enthält Leistungsdaten, die durch Einblicke gesammelt werden, z. b. Azure Monitor für VMS und Azure Monitor für Container. Die folgende Abfrage zeigt die stündliche durchschnittliche Prozessorauslastung für mehrere Computer.

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```


[![Ergebnisse von AVG-Beispiel Zusammenfassung](images/tutorial/am-results-summarize-avg.png)](images/tutorial/am-results-summarize-avg.png#lightbox)



## <a name="render-display-a-chart-or-table"></a>Rendering: Diagramm oder Tabelle anzeigen
Der [renderoperator](./renderoperator.md?pivots=azuremonitor) gibt an, wie die Ausgabe der Abfrage gerendert werden soll. Log Analytics Renderingausgabe standardmäßig als Tabelle, und Sie können nach dem Ausführen der Abfrage verschiedene Diagrammtypen auswählen. Der- `render` Operator ist nützlich, um in Abfragen zu enthalten, bei denen ein bestimmter Diagrammtyp normalerweise bevorzugt wird.

Im folgenden Beispiel wird die stündliche durchschnittliche Prozessorauslastung für einen einzelnen Computer angezeigt und die Ausgabe als Zeitdiagramm gerendert.

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

[![Ergebnisse des Rendering-Beispiels](images/tutorial/am-results-render.png)](images/tutorial/am-results-render.png#lightbox)



## <a name="multiple-series"></a>Mehrere Reihen
Wenn mehrere Werte in einer-Klausel vorhanden sind `summarize by` , zeigt das Diagramm eine separate Reihe für jeden Satz von Werten an:

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```


[![Ergebnis des Beispiels "Rendering mit mehreren Reihen"](images/tutorial/am-results-render-multiple.png)](images/tutorial/am-results-render-multiple.png#lightbox)

## <a name="join-data-from-two-tables"></a>Verknüpfen von Daten aus zwei Tabellen
Was geschieht, wenn Sie Daten aus zwei Tabellen in einer einzelnen Abfrage abrufen müssen? Der [Join](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) -Operator ermöglicht es Ihnen, Zeilen aus mehreren Tabellen in einem einzelnen Resultset zu kombinieren. Jede Tabelle muss eine Spalte mit einem übereinstimmenden Wert aufweisen, damit der Join erkennt, welche Zeilen übereinstimmen müssen.

[Vmcomputer](/azure/azure-monitor/reference/tables/vmcomputer) ist eine Tabelle, die von Azure Monitor für VMS zum Speichern von Details zu virtuellen Maschinen verwendet wird, die Sie überwacht. [Insighungmetrics](/azure/azure-monitor/reference/tables/insightsmetrics) enthält Leistungsdaten, die von diesen virtuellen Computern gesammelt werden. Ein in *insightionmetrics* erfasster Wert ist der verfügbare Arbeitsspeicher, jedoch nicht der prozentuale Speicher. Um den Prozentsatz zu berechnen, benötigen wir den physischen Speicher für jeden virtuellen Computer in *vmcomputer*.

In der folgenden Beispiel Abfrage wird ein Join verwendet, um diese Berechnung auszuführen. Die unter [schiedliche](/azure/data-explorer/kusto/query/joinoperator) wird mit *vmcomputer* verwendet, da Details regelmäßig von jedem Computer gesammelt werden, der mehrere Zeilen für jede in dieser Tabelle erstellt. Die beiden Tabellen werden mithilfe der Spalte *Computer* verknüpft. Dies bedeutet, dass im Resultset eine Zeile erstellt wird, die Spalten aus beiden Tabellen für jede Zeile in *insightsmetrics* mit einem Wert in *Computer* enthält, der dem gleichen Wert in der Spalte *Computer* in *vmcomputer* entspricht.

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

[![Ergebnisse von Join-Beispiel](images/tutorial/am-results-join.png)](images/tutorial/am-results-join.png#lightbox)


## <a name="let-assign-a-result-to-a-variable"></a>let: Zuweisen eines Ergebnisses zu einer Variablen
Verwenden [Sie Let](./letstatement.md) , um das Lesen und Verwalten von Abfragen zu vereinfachen. Mit diesem Operator können Sie die Ergebnisse einer Abfrage einer Variablen zuweisen, die Sie später verwenden können. Die gleiche Abfrage im vorherigen Beispiel könnte wie folgt umgeschrieben werden.

 
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

[![Ergebnisse von Let-Beispiel](images/tutorial/am-results-let.png)](images/tutorial/am-results-let.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen von Codebeispielen für die Kusto-Abfragesprache](samples.md?pivots=azuremonitor).


::: zone-end
