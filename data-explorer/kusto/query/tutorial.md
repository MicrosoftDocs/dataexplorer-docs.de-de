---
title: 'Tutorial: Azure Daten-Explorer'
description: Dieser Artikel beschreibt das Tutorial in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 2060d2996338cf1eee33b5905e9929c46040afa9
ms.sourcegitcommit: b286703209f1b657ac3d81b01686940f58e5e145
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86188590"
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

So sieht das Ergebnis aus:

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

|StartTime|EndTime|Zustand|EventType|Episodenarrative|
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

|StartTime|EndTime|EventType|Zustand|EventNarrative|
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

|StartTime|EndTime|EventType|Zustand|EventNarrative|
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
| project  StartTime, EndLat, EventType, EventNarrative
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

|StartTime|EndTime|Dauer|EventType|Zustand|
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

|Zustand|Stormcount|Typeofstorms|
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

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
