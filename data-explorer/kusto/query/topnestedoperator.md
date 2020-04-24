---
title: Top-verschachtelter Operator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der top-verschachtelte Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 296c36f4653bec971c71dc210008af7b0d98959a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505932"
---
# <a name="top-nested-operator"></a>top-nested operator

Erzeugt oberste, hierarchische Ergebnisse, wobei jede Ebene einen Drilldown, basierend auf Werten der vorherigen Ebene, darstellt. 

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

Es ist nützlich für Dashboard-Visualisierungsszenarien, oder wenn es notwendig ist, eine Frage zu beantworten, die wie folgt klingt: "Finden Sie, was Top-N-Werte von K1 sind (mit einer Aggregation); für jeden von ihnen, finden Sie, was sind die Top-M-Werte von K2 (mit einer anderen Aggregation); ..."

**Syntax**

*T* `|` `desc``,``asc` |  `=``with others=` `of` *Expression1* `by` *N1* *Aggregation1* *ConstExpr1**AggName1* [ N1 ] Expression1 [ ConstExpr1 ] [ AggName1 ] Aggregation1 [ ] [ ...] `top-nested`

**Argumente**

für jede Top-Nested-Regel:
* *N1*: Die Anzahl der obersten Werte, die für jede Hierarchieebene zurückgegeben werden sollen. Optional (wenn nicht angegeben, werden alle unterschiedlichen Werte zurückgegeben).
* *Ausdruck1*: Ein Ausdruck, mit dem die obersten Werte ausgewählt werden sollen. In der Regel handelt es *T*sich entweder um einen Spaltennamen in `bin()`T oder um einen Binning-Vorgang (z. B. ) in einer solchen Spalte. 
* *ConstExpr1*: Wenn angegeben, wird für die entsprechende Verschachtelungsebene eine zusätzliche Zeile angehängt, die das aggregierte Ergebnis für die anderen Werte enthält, die nicht in den topen Werten enthalten sind.
* *Aggregation1*: Ein Aufruf einer Aggregationsfunktion, die einer von: [sum()](sum-aggfunction.md), [count()](count-aggfunction.md), [max()](max-aggfunction.md), [min()](min-aggfunction.md), [dcount()](dcountif-aggfunction.md), [avg()](avg-aggfunction.md), [perperile()](percentiles-aggfunction.md), [percentilew()](percentiles-aggfunction.md)oder einer beliebigen Algebric-Kombination dieser Aggregationen sein kann.
* Unter Umständen wird `asc` oder `desc` (Standard) angezeigt, um zu steuern, ob die tatsächliche Auswahl vom „unteren“ oder „oberen“ Ende des Bereichs erfolgt.

**Rückgabe**

Hierarchische Tabelle, die Eingabespalten und für jede spalteenthält, wird eine neue Spalte erstellt, um das Ergebnis der Aggregation für die gleiche Ebene für jedes Element einzuschließen.
Die Spalten sind in der gleichen Reihenfolge wie die Eingabespalten angeordnet, und die neu erstellte Spalte befindet sich in der Nähe der aggregierten Spalte. Jeder Datensatz hat eine hierarchische Struktur, in der jeder Wert ausgewählt wird, nachdem alle vorherigen Top-Nested-Regeln auf alle vorherigen Ebenen angewendet und dann die Regel der aktuellen Ebene auf diese Ausgabe angewendet wurden.
Dies bedeutet, dass die obersten n Werte für Stufe i für jeden Wert in Stufe i - 1 berechnet werden.
 
**Tipps**

* Verwenden von Spalten, die für *Aggregationsergebnisse* umbenennen: T | top-nested 3 von Location by MachinesNumberForLocation = sum(MachinesNumber) ... .

* Die Anzahl der zurückgegebenen Datensätze kann recht groß sein. bis (*N1*+1) \* (*N2*+1) \* ... \* (*Nm*+1) (wobei m die Anzahl der Ebenen und *Ni* die höchste Anzahl für Stufe i ist).

* Die Aggregation muss eine numerische Spalte mit Aggregationsfunktion empfangen, die eine der oben genannten ist.

* Verwenden `with others=` Sie die Option, um den aggregierten Wert aller anderen Werte abzubekommen, die in einer Ebene nicht die höchsten N-Werte waren.

* Wenn Sie nicht daran `with others=` interessiert sind, für eine gewisse Ebene zu erhalten, werden NULL-Werte angehängt (für die aggreagated Spalte und den Ebenenschlüssel, siehe Beispiel unten).


* Es ist möglich, zusätzliche Spalten für die ausgewählten Top-Nested-Kandidaten zurückzugeben, indem zusätzliche top-nested-Anweisungen wie diese angehängt werden (siehe Beispiele unten):

```kusto
top-nested 2 of ...., ..., ..., top-nested of <additionalRequiredColumn1> by max(1), top-nested of <additionalRequiredColumn2> by max(1)
```

**Beispiel**

```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|State|aggregated_State|`Source`|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|Kansas|87771.2355000001|Strafverfolgungsbehörden|18744.823|FT SCOTT|264.858|
|Kansas|87771.2355000001|Öffentlich|22855.6206|BUCKLIN|488.2457|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279.7083|SHARON SPGS|388.7404|
|TEXAS|123400.5101|Öffentlich|13650.9079|Amarillo|246.2598|
|TEXAS|123400.5101|Strafverfolgungsbehörden|37228.5966|PERRYTON|289.3178|
|TEXAS|123400.5101|Ausgebildeter „Spotter“|13997.7124|Claude|421.44|


* Mit anderen Beispiel:

```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|State|aggregated_State|`Source`|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|Kansas|87771.2355000001|Strafverfolgungsbehörden|18744.823|FT SCOTT|264.858|
|Kansas|87771.2355000001|Öffentlich|22855.6206|BUCKLIN|488.2457|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279.7083|SHARON SPGS|388.7404|
|TEXAS|123400.5101|Öffentlich|13650.9079|Amarillo|246.2598|
|TEXAS|123400.5101|Strafverfolgungsbehörden|37228.5966|PERRYTON|289.3178|
|TEXAS|123400.5101|Ausgebildeter „Spotter“|13997.7124|Claude|421.44|
|Kansas|87771.2355000001|Strafverfolgungsbehörden|18744.823|Alle anderen Endstandorte|18479.965|
|Kansas|87771.2355000001|Öffentlich|22855.6206|Alle anderen Endstandorte|22367.3749|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279.7083|Alle anderen Endstandorte|20890.9679|
|TEXAS|123400.5101|Öffentlich|13650.9079|Alle anderen Endstandorte|13404.6481|
|TEXAS|123400.5101|Strafverfolgungsbehörden|37228.5966|Alle anderen Endstandorte|36939.2788|
|TEXAS|123400.5101|Ausgebildeter „Spotter“|13997.7124|Alle anderen Endstandorte|13576.2724|
|Kansas|87771.2355000001|||Alle anderen Endstandorte|24891.0836|
|TEXAS|123400.5101|||Alle anderen Endstandorte|58523.2932000001|
|Alle anderen Staaten|1149279.5923|||Alle anderen Endstandorte|1149279.5923|


Die folgende Abfrage zeigt die gleichen Ergebnisse für die erste Ebene, die im obigen Beispiel verwendet wurde:

```kusto
 StormEvents
 | where State !in ('TEXAS', 'KANSAS')
 | summarize sum(BeginLat)
```

|sum_BeginLat|
|---|
|1149279.5923|


Anfordern einer weiteren Spalte (EventType) für das top-verschachtelte Ergebnis: 

```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|State|aggregated_State|`Source`|aggregated_Source|EndLocation|aggregated_EndLocation|EventType|
|---|---|---|---|---|---|---|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279.7083|SHARON SPGS|388.7404|Sturm|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279.7083|SHARON SPGS|388.7404|Hagel|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279.7083|SHARON SPGS|388.7404|Tornado|
|Kansas|87771.2355000001|Öffentlich|22855.6206|BUCKLIN|488.2457|Hagel|
|Kansas|87771.2355000001|Öffentlich|22855.6206|BUCKLIN|488.2457|Sturm|
|Kansas|87771.2355000001|Öffentlich|22855.6206|BUCKLIN|488.2457|Hochwasser|
|TEXAS|123400.5101|Ausgebildeter „Spotter“|13997.7124|Claude|421.44|Hagel|
|TEXAS|123400.5101|Strafverfolgungsbehörden|37228.5966|PERRYTON|289.3178|Hagel|
|TEXAS|123400.5101|Strafverfolgungsbehörden|37228.5966|PERRYTON|289.3178|Hochwasser|
|TEXAS|123400.5101|Strafverfolgungsbehörden|37228.5966|PERRYTON|289.3178|Überschwemmung|

Um das Ergebnis nach der letzten geschachtelten Ebene zu sortieren (in diesem Beispiel nach EndLocation) und geben Sie eine Indexsortierreihenfolge für jeden Wert in dieser Ebene (pro Gruppe) an:

```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|State|`Source`|EndLocations|endLocationSums|Indizien|
|---|---|---|---|---|
|TEXAS|Ausgebildeter „Spotter“|Claude|421.44|0|
|TEXAS|Ausgebildeter „Spotter“|Amarillo|316.8892|1|
|TEXAS|Ausgebildeter „Spotter“|DALHART|252.6186|2|
|TEXAS|Ausgebildeter „Spotter“|PERRYTON|216.7826|3|
|TEXAS|Strafverfolgungsbehörden|PERRYTON|289.3178|0|
|TEXAS|Strafverfolgungsbehörden|Leakey|267.9825|1|
|TEXAS|Strafverfolgungsbehörden|BRACKETTVILLE|264.3483|2|
|TEXAS|Strafverfolgungsbehörden|Gilmer|261.9068|3|
|Kansas|Ausgebildeter „Spotter“|SHARON SPGS|388.7404|0|
|Kansas|Ausgebildeter „Spotter“|Atwood|358.6136|1|
|Kansas|Ausgebildeter „Spotter“|Lenora|317.0718|2|
|Kansas|Ausgebildeter „Spotter“|SCOTT CITY|307.84|3|
|Kansas|Öffentlich|BUCKLIN|488.2457|0|
|Kansas|Öffentlich|Ashland|446.4218|1|
|Kansas|Öffentlich|Schutz|446.11|2|
|Kansas|Öffentlich|MEADE STATE PARK|371.1|3|