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
ms.openlocfilehash: a2a8f4fa92a7b8722097ec3595674b855a90f216
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294660"
---
# <a name="top-nested-operator"></a>top-nested operator

Erzeugt eine hierarchische Aggregations-und Top Values-Auswahl, wobei jede Ebene eine Verfeinerung der vorherigen Ebene ist.

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

Der `top-nested` -Operator akzeptiert Tabellendaten als Eingabe und mindestens eine Aggregations Klausel.
Die erste Aggregations Klausel (linksbündig) unterteilt die Eingabedaten Sätze gemäß den eindeutigen Werten eines Ausdrucks über diese Datensätze in Partitionen. Die-Klausel speichert dann eine bestimmte Anzahl von Datensätzen, die diesen Ausdruck für die Datensätze maximieren oder minimieren. Die nächste Aggregations Klausel wendet dann eine ähnliche Funktion in einer geschachtelte-Funktion an. Jede folgende Klausel wird auf die Partition angewendet, die von der vorherigen Klausel erstellt wurde. Dieser Vorgang wird für alle Aggregations Klauseln fortgesetzt.

Der-Operator kann z. b. `top-nested` verwendet werden, um die folgende Frage zu beantworten: "für eine Tabelle mit Umsatzzahlen, wie z. b. Land, Vertriebsmitarbeiter und verkauftem Betrag: Was sind die fünf größten Länder nach Verkäufen? Was sind die ersten drei Vertriebsmitarbeiter in jedem dieser Länder? "

**Syntax**

*T* `|` `top-nested` *TopNestedClause2* [ `,` *TopNestedClause2*...]

Where *topnetstedclause* weist die folgende Syntax auf:

[*N*] `of` [ *`ExprName`* `=` ] *`Expr`* [] [] `with` `others` `=` *`ConstExpr`* `by` *`AggName`* `=` *`Aggregation`* [ `asc`  |  `desc` ]

**Argumente**

Für jede *topnetstedclause*:

* *`N`*: Ein Literaltyp `long` , der angibt, wie viele Top-Werte für diese Hierarchieebene zurückgegeben werden.
  Wenn der Wert weggelassen wird, werden alle unterschiedlichen Werte zurückgegeben.

* *`ExprName`*: Wenn angegeben, wird der Name der Ausgabe Spalte festgelegt, die den Werten von entspricht *`Expr`* .

* *`Expr`*: Ein Ausdruck über den Eingabedaten Satz, der angibt, welcher Wert für diese Hierarchieebene zurückgegeben werden soll.
  In der Regel handelt es sich um einen Spalten Verweis für die tabellarische Eingabe (*T*) oder eine Berechnung (z `bin()` . b.) über eine solche Spalte.

* *`ConstExpr`*: Wenn dieser Wert angegeben wird, wird für jeden Hierarchieebene 1-Datensatz mit dem Wert hinzugefügt, der die Aggregation für alle Datensätze ist, deren Anfang nicht war.

* *`AggName`*: Wenn dieser Wert angegeben ist, legt dieser Bezeichner den Spaltennamen in der Ausgabe für den Wert der *Aggregation*fest.

* *`Aggregation`*: Ein numerischer Ausdruck, der die Aggregation angibt, die auf alle Datensätze angewendet werden soll, die denselben Wert haben *`Expr`* . Der Wert dieser Aggregation bestimmt, welche der resultierenden Datensätze "Top" sind.
  
  Die folgenden Aggregations Funktionen werden unterstützt:
   * [Sum ()](sum-aggfunction.md),
   * [count ()](count-aggfunction.md),
   * [Max ()](max-aggfunction.md),
   * [Min ()](min-aggfunction.md),
   * [DCount ()](dcountif-aggfunction.md),
   * [AVG ()](avg-aggfunction.md),
   * [Perzentil ()](percentiles-aggfunction.md), und
   * [percentilew ()](percentiles-aggfunction.md). Jede algebraische Kombination der Aggregationen wird ebenfalls unterstützt.

* `asc`oder `desc` (der Standardwert) wird möglicherweise angezeigt, um zu steuern, ob die Auswahl tatsächlich von der "untersten" oder "Top" des Bereichs der aggregierten Werte ist.

**Rückgabe**

Dieser Operator gibt eine Tabelle mit zwei Spalten für jede Aggregations Klausel zurück:

* Eine Spalte enthält die unterschiedlichen Werte der Berechnung der Klausel *`Expr`* (mit dem Spaltennamen " *exprname* ", falls angegeben).

* Eine Spalte enthält das Ergebnis der *Aggregations* Berechnung (bei Angabe des Spaltennamens *aggregationname* ).

**Kommentare**

Eingabe Spalten, die nicht als Werte angegeben werden, werden *`Expr`* nicht ausgegeben.
Um alle Werte auf einer bestimmten Ebene zu erhalten, fügen Sie eine Anzahl von Aggregationen hinzu:

* Lässt den Wert von *N* aus.
* Verwendet den Spaltennamen als Wert von.*`Expr`*
* Verwendet `Ignore=max(1)` als Aggregation und ignoriert die Spalte anschließend (oder entfernt Sie) `Ignore` .

Die Anzahl der Datensätze vergrößert sich möglicherweise exponentiell mit der Anzahl von Aggregations Klauseln ((N1 + 1) \* (N2 + 1) \* ...). Die Daten Satz Vergrößerung ist noch schneller, wenn kein *N* -Limit angegeben wird. Berücksichtigen Sie, dass dieser Operator möglicherweise eine beträchtliche Menge an Ressourcen beansprucht.

In Fällen, in denen die Verteilung der Aggregation beträchtlich nicht einheitlich ist, begrenzen Sie die Anzahl der unterschiedlichen Werte, die zurückgegeben werden (mithilfe von *N*), und verwenden Sie die `with others=` Option *constexpr* , um eine Angabe für die Gewichtung aller anderen Fälle zu erhalten.

**Beispiele**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|Zustand|aggregated_State|`Source`|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|Kansas|87771.2355000001|Strafverfolgungsbehörden|18744,823|FT Scott|264,858|
|Kansas|87771.2355000001|Öffentlich|22855,6206|Bucklin|488,2457|
|Kansas|87771.2355000001|Ausgebildeter „Spotter“|21279,7083|Sharon-Spgs|388,7404|
|TEXAS|123400,5101|Öffentlich|13650,9079|Amarillo|246,2598|
|TEXAS|123400,5101|Strafverfolgungsbehörden|37228,5966|Perryton|289,3178|
|TEXAS|123400,5101|Ausgebildeter „Spotter“|13997,7124|Claude|421,44|

Verwenden Sie die Option "with others":

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|Zustand|aggregated_State|`Source`|aggregated_Source|EndLocation|aggregated_EndLocation|
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


Fordern Sie eine weitere Spalte (EventType) für das Top-netsted-Ergebnis an: 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|Zustand|aggregated_State|`Source`|aggregated_Source|EndLocation|aggregated_EndLocation|EventType|
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

Geben Sie für jeden Wert auf dieser Ebene (pro Gruppe) eine Sortierreihenfolge für den Index an, um das Ergebnis nach der letzten auf der Ebene (in diesem Beispiel nach endLocation) zu sortieren:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|Zustand|`Source`|Endpunkte|endlocationsums|Indizes|
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
