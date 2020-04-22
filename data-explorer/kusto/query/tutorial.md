---
title: Tutorial - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird das Tutorial in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f8a5a7b8f96fdd75ccff5dcfe8499ab98a2d4380
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663339"
---
# <a name="tutorial"></a>Lernprogramm

::: zone pivot="azuredataexplorer"

Der beste Weg, um mehr über die Kusto-Abfragesprache zu erfahren, besteht darin, einige einfache Abfragen zu betrachten, um das "Gefühl" für die Sprache mithilfe einer [Datenbank mit einigen Beispieldaten](https://help.kusto.windows.net/Samples)zu erhalten. Die in diesem Artikel gezeigten Abfragen sollten in dieser Datenbank ausgeführt werden. Die `StormEvents` Tabelle in dieser Beispieldatenbank enthält einige Informationen zu Stürmen, die in den USA aufgetreten sind.

<!--
  TODO: Provide link to reference data we used originally in StormEvents
-->

<!--
  TODO: A few samples below reference non-existent tables, such as Events and Logs.
        We need to add these tables.
-->

## <a name="count-rows"></a>Zeilen zählen

Unsere Beispieldatenbank verfügt `StormEvents`über eine Tabelle mit dem Namen .
Um herauszufinden, wie groß es ist, werden wir seinen Inhalt in einen Operator einfließen lassen, der einfach die Zeilen zählt:

* *Syntax:* Eine Abfrage ist eine Datenquelle (in der Regel ein Tabellenname), optional gefolgt von einem oder mehreren Paaren des Pipe-Zeichens und einem tabellarischen Operator.

```kusto
StormEvents | count
```

Das Ergebnis lautet wie folgt:

|Anzahl|
|-----|
|59066|
    
[Zähloperator](./countoperator.md).

## <a name="project-select-a-subset-of-columns"></a>Projekt: Wählen Sie eine Teilmenge von Spalten aus

Verwenden Sie [Projekt,](./projectoperator.md) um nur die gewünschten Spalten auszuwählen. Siehe Beispiel unten, das sowohl [Projekt-](./projectoperator.md) als auch [Take-Operator](./takeoperator.md) verwendet.

## <a name="where-filtering-by-a-boolean-expression"></a>Wo: Filtern nach einem booleschen Ausdruck

Sehen wir uns `flood`nur `California` die s in während Feb-2007:

```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

|StartTime|EndTime|State|EventType|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|Kalifornien|Hochwasser|Ein Frontalsystem, das sich über das südliche San Joaquin Valley bewegte, brachte in den frühen Morgenstunden des 19. Kleinere Überschwemmungen wurden über den State Highway 166 in der Nähe von Taft gemeldet.|

## <a name="take-show-me-n-rows"></a>nehmen: zeige mir n Zeilen

Betrachten Sie einige Daten: Was sehen Sie in 5 Beispielzeilen?

```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|State|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Heavy Rain|Florida|In einem Zeitraum von 24 Stunden fielen in Teilen des Küstenkreises Volusia bis zu 9 Zoll Regen.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Tornado|Florida|Ein Tornado traf in der Stadt Eustis am nördlichen Ende des West Crooked Lake. Der Tornado intensivierte sich schnell zu EF1-Stärke, als er sich nach Nordwesten durch Eustis bewegte. Die Strecke war knapp zwei Meilen lang und hatte eine maximale Breite von 300 Metern.  Der Tornado zerstörte 7 Häuser. 27 Häuser erlitten große Schäden, 81 Häuser meldeten kleinere Schäden. Es gab keine schweren Verletzungen und Sachschaden wurde auf 6,2 Millionen US-Dollar festgesetzt.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|Wasserauslauf|ATLANTIC SOUTH|Im Atlantik südöstlich von Melbourne Beach bildete sich ein Wasserauslauf, der sich kurzzeitig in Richtung Küste bewegte.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Sturm|Mississippi|Zahlreiche große Bäume wurden mit einigen heruntergehauen. Schäden traten im östlichen Adams County auf.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Sturm|Georgien|Die County-Dispatch berichtete, dass mehrere Bäume entlang Quincey Batten Loop in der Nähe der State Road 206 umgestürzt wurden. Die Kosten für die Entfernung von Bäumen wurden geschätzt.|

Aber [take](./takeoperator.md) zeigt Zeilen aus der Tabelle in keiner bestimmten Reihenfolge an, also lassen Sie uns sie sortieren.
* [limit](./takeoperator.md) ist ein Alias für [Take](./takeoperator.md) und hat den gleichen Effekt.

## <a name="sort-and-top"></a>Sortieren und oben

* *Syntax:* Einige Operatoren verfügen über Parameter, die durch Schlüsselwörter wie `by`eingeführt werden.
* `desc` = absteigende Reihenfolge, `asc` = aufsteigende Reihenfolge.

Zeigen Sie die ersten n Zeilen, sortiert nach einer bestimmten Spalte an:

```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|State|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Wintersturm|MICHIGAN|Dieses schwere Schneeereignis setzte sich am Neujahrstag bis in die frühen Morgenstunden fort.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Wintersturm|MICHIGAN|Dieses schwere Schneeereignis setzte sich am Neujahrstag bis in die frühen Morgenstunden fort.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Wintersturm|MICHIGAN|Dieses schwere Schneeereignis setzte sich am Neujahrstag bis in die frühen Morgenstunden fort.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Hoher Wind|Kalifornien|Nord- bis Nordostwinde böten bis etwa 58 mph in den Bergen des Ventura County.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Hoher Wind|Kalifornien|Der Warm Springs RAWS Sensor meldete Nordwinde mit Böen von bis zu 58 mph.|

Das gleiche kann durch die Verwendung von [Sortierung](./sortoperator.md) und dann [nehmen](./takeoperator.md) Operator erreicht werden

```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndLat, EventType, EventNarrative
```

## <a name="extend-compute-derived-columns"></a>Erweitern: abgeleitete Spalten berechnen

Erstellen Sie eine neue Spalte, indem Sie einen Wert in jeder Zeile berechnen:

```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

|StartTime|EndTime|Duration|EventType|State|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Heavy Rain|Florida|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Tornado|Florida|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|Wasserauslauf|ATLANTIC SOUTH|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Sturm|Mississippi|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Sturm|Georgien|

Es ist möglich, den Spaltennamen wiederzuverwenden und der gleichen Spalte das Berechnungsergebnis zuzuweisen.
Beispiel:

```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

|x|y|
|---|---|
|3|1|

[Skalare Ausdrücke](./scalar-data-types/index.md) können alle üblichen`+` `-`Operatoren `/` `%`( , , `*`, , , ) enthalten, und es gibt eine Reihe nützlicher Funktionen.

## <a name="summarize-aggregate-groups-of-rows"></a>Zusammenfassend: Aggregatgruppen von Zeilen

Zählen Sie, wie viele Veranstaltungen aus jedem Land kommen:

```kusto
StormEvents
| summarize event_count = count() by State
```

[fasst](./summarizeoperator.md) Zeilen zusammen, die die `by` gleichen Werte in der Klausel haben, und verwendet dann die Aggregationsfunktion (z. B.), `count`um jede Gruppe in einer einzigen Zeile zu kombinieren. In diesem Fall gibt es also eine Zeile für jeden Status und eine Spalte für die Anzahl der Zeilen in diesem Zustand.

Es gibt eine Reihe von [Aggregationsfunktionen](./summarizeoperator.md#list-of-aggregation-functions), und Sie können mehrere von ihnen in einem Zusammengefasstoperator verwenden, um mehrere berechnete Spalten zu erstellen. Zum Beispiel könnten wir die Anzahl der Stürme in jedem Bundesstaat und auch eine Summe der einzigartigen Art von Stürmen pro Zustand erhalten,  
dann könnten wir [top](./topoperator.md) verwenden, um die am stärksten vom Sturm betroffenen Zustände zu erhalten:

```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

|State|StormCount|TypeOfStorms|
|---|---|---|
|TEXAS|4701|27|
|Kansas|3166|21|
|IOWA|2337|19|
|Illinois|2022|23|
|Missouri|2016|20|

Das Ergebnis von „summarize“ verfügt über Folgendes:

* jede in `by`genannte Spalte
* eine Spalte für jeden berechneten Ausdruck;
* eine Zeile für jede Kombination von `by` -Werten

## <a name="summarize-by-scalar-values"></a>Zusammenfassen nach Skalarwerten

Sie können skalare Werte (numerische Werte, Zeit `by` oder Intervall) in der Klausel verwenden, aber Sie sollten die Werte in Lagerplätze einzahlen.  
Die [bin()-Funktion](./binfunction.md) ist hierfür nützlich:

```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

Dadurch werden alle Zeitstempel auf Intervalle von 1 Tag reduziert:

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

Der [bin()](./binfunction.md) ist in vielen Sprachen identisch mit der [Floor()-Funktion.](./floorfunction.md) Es reduziert einfach jeden Wert auf das nächste Vielfache des Moduls, das Sie bereitstellen, sodass diese [Zusammenfassung](./summarizeoperator.md) die Zeilen Gruppen zuweisen kann.

## <a name="render-display-a-chart-or-table"></a>Rendern: Anzeigen eines Diagramms oder einer Tabelle

Projizieren Sie zwei Spalten, und verwenden Sie sie als x- und y-Achse eines Diagramms:

```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tour/060.png" alt-text="060":::

Obwohl wir `mid` im Projektbetrieb entfernt haben, brauchen wir es immer noch, wenn wir wollen, dass das Diagramm die Länder in dieser Reihenfolge anzeigt.

Streng genommen ist "Render" ein Merkmal des Clients und nicht Teil der Abfragesprache. Dennoch ist es in die Sprache integriert und ist sehr nützlich, um sich Ihre Ergebnisse vorzustellen.


## <a name="timecharts"></a>Zeitdiagramme

Wenn wir zu numerischen Abschnitten zurückkehren, zeigen wir eine Zeitreihe an:

```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tour/080.png" alt-text="080":::

## <a name="multiple-series"></a>Mehrere Reihen

Verwenden Sie mehrere Werte in einer `summarize by` -Klausel, um eine separate Zeile für jede Kombination von Werten zu erstellen:

```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tour/090.png" alt-text="090":::

Fügen Sie einfach den Renderbegriff zu den oben genannten hinzu: `| render timechart`.

:::image type="content" source="images/tour/100.png" alt-text="100":::

Beachten `render timechart` Sie, dass die erste Spalte als x-Achse verwendet wird, und zeigt dann die anderen Spalten als separate Linien an.

## <a name="daily-average-cycle"></a>Durchschnittlicher Tageszyklus

Wie schwankt die Aktivität über den durchschnittlichen Tag?

Zählen Sie Ereignisse nach der Zeit modulo eines Tages, in Stunden eingebunden. Beachten Sie, `floor` dass wir anstelle von Bin verwenden:

```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tour/120.png" alt-text="120":::

Derzeit `render` wird die Dauer nicht richtig gekennzeichnet, `| render columnchart` aber wir könnten stattdessen verwenden:

:::image type="content" source="images/tour/110.png" alt-text="110":::

## <a name="compare-multiple-daily-series"></a>Vergleichen mehrerer täglicher Reihen

Wie schwankt die Aktivität über die Tageszeit in verschiedenen Zuständen?

```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tour/130.png" alt-text="130":::

Dividieren Sie durch, `1h` um die x-Achse in Stundenzahl anstelle einer Dauer zu drehen:

```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tour/140.png" alt-text="140":::

## <a name="join"></a>join

Wie finde ich für zwei gegebene EventTypes, in welchem Zustand beide aufgetreten sind?

Sie können Sturmereignisse mit dem ersten EventType und mit dem zweiten EventType abrufen und dann den beiden Sätzen im Status beitreten.

```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tour/145.png" alt-text="145":::

## <a name="user-session-example-of-join"></a>Beispiel für Benutzersitzungen für die Verknüpfung

In diesem Abschnitt `StormEvents` wird die Tabelle nicht verwendet.

Angenommen, Sie verfügen über Daten, die Ereignisse enthalten, die den Anfang und das Ende jeder Benutzersitzung markieren, mit einer eindeutigen ID für jede Sitzung. 

Wie lange dauert jede Benutzersitzung?

Wenn `extend` Sie einen Alias für die beiden Zeitstempel bereitstellen, können Sie die Sitzungsdauer berechnen.

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

:::image type="content" source="images/tour/150.png" alt-text="150":::

Es ist üblich, `project` zu verwenden, um vor dem Verknüpfen nur die Spalten auszuwählen, die wir benötigen.
In den gleichen Klauseln benennen wir die Zeitstempelspalte um.

## <a name="plot-a-distribution"></a>Darstellen einer Verteilung

Wie viele Stürme gibt es unterschiedlicher Länge?

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

:::image type="content" source="images/tour/170.png" alt-text="170":::

Oder `| render columnchart`verwenden Sie :

:::image type="content" source="images/tour/160.png" alt-text="160":::

## <a name="percentiles"></a>Perzentile

Welche Dauerbereiche decken unterschiedliche Prozentsätze von Stürmen ab?

Verwenden Sie die obige Abfrage, aber ersetzen Sie sie durch: `render`

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

In diesem Fall haben `by` wir keine Klausel bereitgestellt, so dass das Ergebnis eine einzelne Zeile ist:

:::image type="content" source="images/tour/180.png" alt-text="180":::

Daraus lässt sich Folgendes ablesen:

* 5% der Stürme haben eine Dauer von weniger als 5m;
* 50% der Stürme dauern weniger als 1h 25m;
* 5% der Stürme dauern mindestens 2h 50m.

Um eine separate Aufschlüsselung für jeden Zustand zu erhalten, müssen wir nur die Zustandsspalte getrennt durch beide zusammenfassenden Operatoren bringen:

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

:::image type="content" source="images/tour/190.png" alt-text="190":::

## <a name="let-assign-a-result-to-a-variable"></a>let: Zuweisen eines Ergebnisses zu einer Variablen

Verwenden Sie [let,](./letstatement.md) um die Teile des Abfrageausdrucks im obigen Beispiel "Join" zu trennen. Die Ergebnisse sind wie folgt unverändert:

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

> Tipp: Legen Sie im Kusto-Client keine leeren Zeilen zwischen die Teile davon. Stellen Sie sicher, dass Sie alles ausführen.

## <a name="combining-data-from-several-databases-in-a-query"></a>Kombinieren von Daten aus mehreren Datenbanken in einer Abfrage

Ausführliche Informationen zu [datenbankübergreifenden Abfragen](./cross-cluster-or-database-queries.md) finden Sie unter

Wenn Sie eine Abfrage des Stils schreiben:

```kusto
Logs | where ...
```

Die Tabelle mit dem Namen Logs muss sich in der Standarddatenbank befinden. Wenn Sie auf Tabellen aus einer anderen Datenbank zugreifen möchten, verwenden Sie die folgende Syntax:

```kusto
database("db").Table
```

Wenn Sie also über Datenbanken mit dem Namen *Diagnostics* and *Telemetry* verfügen und einige ihrer Daten korrelieren möchten, können Sie schreiben (vorausgesetzt, *Diagnostics* ist Ihre Standarddatenbank)

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

oder wenn Ihre Standarddatenbank *Telemetrie* ist

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
Alle oben genannten Annahmen, dass sich beide Datenbanken in dem Cluster befinden, mit dem Sie derzeit verbunden sind. Angenommen, die *Telemetriedatenbank* gehörte zu einem anderen Cluster mit dem Namen *TelemetryCluster.kusto.windows.net* dann benötigen Sie für den Zugriff darauf.

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> Hinweis: Wenn der Cluster angegeben ist, ist die Datenbank obligatorisch

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end