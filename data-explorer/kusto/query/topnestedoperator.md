---
title: 'Top-netsted-Operator: Azure Daten-Explorer'
description: In diesem Artikel wird der Top-netsted-Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f87606442dcc5d7c9e7e0fceec379c37169757c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370765"
---
# <a name="top-nested-operator"></a>top-nested operator

Erzeugt oberste, hierarchische Ergebnisse, wobei jede Ebene einen Drilldown, basierend auf Werten der vorherigen Ebene, darstellt. 

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

Dies ist nützlich für dashboardvisualisierungs Szenarien oder, wenn es erforderlich ist, auf eine Frage zu antworten, die wie folgt aussieht: "Suchen nach den Top-N-Werten von K1 (unter Verwendung einiger Aggregationen); Suchen Sie für jeden dieser Elemente die Top-M-Werte von K2 (mit einer anderen Aggregation). ..."

**Syntax**

*T* `|` `top-nested` [*N1*] `of` *expression1* [ `with others=` *ConstExpr1*] `by` [*AggName1* `=` ] *Aggregation1* [ `asc`  |  `desc` ] [ `,` ...]

**Argumente**

für jede Top-netsted-Regel gilt Folgendes:
* *N1*: die Anzahl der Top-Werte, die für jede Hierarchieebene zurückgegeben werden sollen. Optional (wenn kein Wert weggelassen wird, werden alle unterschiedlichen Werte zurückgegeben).
* *Expression1*: ein Ausdruck, nach dem die obersten Werte ausgewählt werden. In der Regel handelt es sich dabei entweder um einen Spaltennamen in *T*oder um einen Klassifizierungs Vorgang (z. b. `bin()` ) in einer solchen Spalte. 
* *ConstExpr1*: Wenn angegeben, wird für die anwendbare Schachtelungs Ebene eine zusätzliche Zeile angehängt, die das aggregierte Ergebnis für die anderen Werte enthält, die nicht in den obersten Werten enthalten sind.
* *Aggregation1*: ein aufrufungs Vorgang einer Aggregations Funktion, bei der es sich um Folgendes handeln kann: [Sum ()](sum-aggfunction.md), [count ()](count-aggfunction.md), [Max ()](max-aggfunction.md), [Min ()](min-aggfunction.md), [DCount ()](dcountif-aggfunction.md), [AVG ()](avg-aggfunction.md), [Percentile ()](percentiles-aggfunction.md), [percentilew ()](percentiles-aggfunction.md)oder eine beliebige algebric-Kombination dieser Aggregationen.
* Unter Umständen wird `asc` oder `desc` (Standard) angezeigt, um zu steuern, ob die tatsächliche Auswahl vom „unteren“ oder „oberen“ Ende des Bereichs erfolgt.

**Rückgabe**

Hierarchische Tabelle mit Eingabe Spalten, und für jede einzelne Spalte wird eine neue Spalte erstellt, um das Ergebnis der Aggregation für die gleiche Ebene für jedes Element einzuschließen.
Die Spalten werden in derselben Reihenfolge der Eingabe Spalten angeordnet, und die neu erstellte Spalte wird in der Nähe der aggregierten Spalte angeordnet. Jeder Datensatz verfügt über eine hierarchische Struktur, in der jeder Wert ausgewählt wird, nachdem alle oben genannten Regeln auf allen vorherigen Ebenen angewendet wurden, und dann die Regel der aktuellen Ebene auf diese Ausgabe angewendet wurde.
Dies bedeutet, dass die obersten n Werte für Ebene i für jeden Wert in Ebene i-1 berechnet werden.
 
**Tipps**

* Spalten umbenennen in für *Aggregations* Ergebnisse verwenden: T | Top-netsted 3 of Location by machinesnumforlocation = Sum (machinesnumber)...

* Die Anzahl der zurückgegebenen Datensätze kann sehr groß sein. bis zu (*N1*+ 1) \* (*N2*+ 1) \* ... \* (*nm*+ 1) (wobei m die Anzahl der Ebenen und *NI* die oberste Anzahl für Ebene i ist).

* Die Aggregation muss eine numerische Spalte mit einer Aggregations Funktion empfangen, bei der es sich um eine der oben genannten handelt.

* Verwenden Sie die- `with others=` Option, um den aggregierten Wert aller anderen Werte zu erhalten, bei denen es sich nicht um die obersten N-Werte auf einer bestimmten Ebene handelt.

* Wenn Sie `with others=` für eine bestimmte Ebene nicht interessiert sind, werden NULL-Werte angehängt (für die aggregierte Spalte und den Levelschlüssel, siehe Beispiel unten).


* Es ist möglich, zusätzliche Spalten für die ausgewählten Top-netsted-Kandidaten zurückzugeben, indem Sie zusätzliche Top-netsted-Anweisungen anfügen (siehe Beispiele unten):

```kusto
top-nested 2 of ...., ..., ..., top-nested of <additionalRequiredColumn1> by max(1), top-nested of <additionalRequiredColumn2> by max(1)
```

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|Bundesland/Kanton|aggregated_State|`Source`|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|Kansas|87771.2355000001|Strafverfolgungsbehörden|18744,823|FT Scott|264,858|
|Kansas|87771.2355000001|Öffentlich|22855,6206|Bucklin|488,2457|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279,7083|Sharon-Spgs|388,7404|
|TEXAS|123400,5101|Öffentlich|13650,9079|Amarillo|246,2598|
|TEXAS|123400,5101|Strafverfolgungsbehörden|37228,5966|Perryton|289,3178|
|TEXAS|123400,5101|Ausgebildeter „Spotter“|13997,7124|Claude|421,44|


* Mit anderen Beispielen:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|Bundesland/Kanton|aggregated_State|`Source`|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|Kansas|87771.2355000001|Strafverfolgungsbehörden|18744,823|FT Scott|264,858|
|Kansas|87771.2355000001|Öffentlich|22855,6206|Bucklin|488,2457|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279,7083|Sharon-Spgs|388,7404|
|TEXAS|123400,5101|Öffentlich|13650,9079|Amarillo|246,2598|
|TEXAS|123400,5101|Strafverfolgungsbehörden|37228,5966|Perryton|289,3178|
|TEXAS|123400,5101|Ausgebildeter „Spotter“|13997,7124|Claude|421,44|
|Kansas|87771.2355000001|Strafverfolgungsbehörden|18744,823|Alle anderen endspeicher Orte|18479,965|
|Kansas|87771.2355000001|Öffentlich|22855,6206|Alle anderen endspeicher Orte|22367,3749|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279,7083|Alle anderen endspeicher Orte|20890,9679|
|TEXAS|123400,5101|Öffentlich|13650,9079|Alle anderen endspeicher Orte|13404,6481|
|TEXAS|123400,5101|Strafverfolgungsbehörden|37228,5966|Alle anderen endspeicher Orte|36939,2788|
|TEXAS|123400,5101|Ausgebildeter „Spotter“|13997,7124|Alle anderen endspeicher Orte|13576,2724|
|Kansas|87771.2355000001|||Alle anderen endspeicher Orte|24891,0836|
|TEXAS|123400,5101|||Alle anderen endspeicher Orte|58523.2932000001|
|Alle anderen Zustände|1149279,5923|||Alle anderen endspeicher Orte|1149279,5923|


Die folgende Abfrage zeigt die gleichen Ergebnisse für die erste Ebene, die im obigen Beispiel verwendet wird:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
 StormEvents
 | where State !in ('TEXAS', 'KANSAS')
 | summarize sum(BeginLat)
```

|sum_BeginLat|
|---|
|1149279,5923|


Eine weitere Spalte (EventType) wird für das am häufigsten eingefügte Ergebnis angefordert: 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|Bundesland/Kanton|aggregated_State|`Source`|aggregated_Source|EndLocation|aggregated_EndLocation|EventType|
|---|---|---|---|---|---|---|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279,7083|Sharon-Spgs|388,7404|Sturm|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279,7083|Sharon-Spgs|388,7404|Hagel|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279,7083|Sharon-Spgs|388,7404|Hüterin|
|Kansas|87771.2355000001|Öffentlich|22855,6206|Bucklin|488,2457|Hagel|
|Kansas|87771.2355000001|Öffentlich|22855,6206|Bucklin|488,2457|Sturm|
|Kansas|87771.2355000001|Öffentlich|22855,6206|Bucklin|488,2457|Hochwasser|
|TEXAS|123400,5101|Ausgebildeter „Spotter“|13997,7124|Claude|421,44|Hagel|
|TEXAS|123400,5101|Strafverfolgungsbehörden|37228,5966|Perryton|289,3178|Hagel|
|TEXAS|123400,5101|Strafverfolgungsbehörden|37228,5966|Perryton|289,3178|Hochwasser|
|TEXAS|123400,5101|Strafverfolgungsbehörden|37228,5966|Perryton|289,3178|Überschwemmung|

Um das Ergebnis nach der letzten (in diesem Beispiel nach endLocation) gruppierten Ebene zu sortieren, und geben Sie für jeden Wert auf dieser Ebene (pro Gruppe) eine Sortierreihenfolge für den Index an:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|Bundesland/Kanton|`Source`|Endpunkte|endlocationsums|Indizes|
|---|---|---|---|---|
|TEXAS|Ausgebildeter „Spotter“|Claude|421,44|0|
|TEXAS|Ausgebildeter „Spotter“|Amarillo|316,8892|1|
|TEXAS|Ausgebildeter „Spotter“|Dalhart|252,6186|2|
|TEXAS|Ausgebildeter „Spotter“|Perryton|216,7826|3|
|TEXAS|Strafverfolgungsbehörden|Perryton|289,3178|0|
|TEXAS|Strafverfolgungsbehörden|Leakey|267,9825|1|
|TEXAS|Strafverfolgungsbehörden|Brackettville|264,3483|2|
|TEXAS|Strafverfolgungsbehörden|Gilmer|261,9068|3|
|Kansas|Ausgebildeter „Spotter“|Sharon-Spgs|388,7404|0|
|Kansas|Ausgebildeter „Spotter“|Atwoods|358,6136|1|
|Kansas|Ausgebildeter „Spotter“|Lenora|317,0718|2|
|Kansas|Ausgebildeter „Spotter“|Scott City|307,84|3|
|Kansas|Öffentlich|Bucklin|488,2457|0|
|Kansas|Öffentlich|Ashland|446,4218|1|
|Kansas|Öffentlich|Recht|446,11|2|
|Kansas|Öffentlich|Meade-Zustands Park|371,1|3|
