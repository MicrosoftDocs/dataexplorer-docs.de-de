---
title: 'Pivot-Plug-in: Azure Daten-Explorer'
description: Dieser Artikel beschreibt das Pivot-Plug-in in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4662b1bd9f68778cab1f799f564499e23add5812
ms.sourcegitcommit: 6a0bd5b84f9bd739510c6a75277dec3a9e851edd
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84788901"
---
# <a name="pivot-plugin"></a>Pivot-Plugin

Rotiert eine Tabelle, indem die eindeutigen Werte aus einer Spalte in der Eingabe Tabelle in mehrere Spalten in der Ausgabe Tabelle umgewandelt werden, und führt Aggregationen aus, bei denen Sie für alle verbleibenden Spaltenwerte erforderlich sind, die in der endgültigen Ausgabe erwünscht sind.

```kusto
T | evaluate pivot(PivotColumn)
```

**Syntax**

`T | evaluate pivot(`*PivotColumn* `[, ` *AggregationFunction* `] [,` *column1* `[,` *Column2* ...`]])`

**Argumente**

* *PivotColumn*: die zu rolligende Spalte. jeder eindeutige Wert aus dieser Spalte ist eine Spalte in der Ausgabe Tabelle.
* *Aggregations Funktion*: (optional) aggregiert mehrere Zeilen in der Eingabe Tabelle in eine einzelne Zeile in der Ausgabe Tabelle. Derzeit unterstützte Funktionen: `min()` , `max()` , `any()` , `sum()` , `dcount()` , `avg()` , `stdev()` , `variance()` , `make_list()` , `make_bag()` , `make_set()` , `count()` (Standardwert ist `count()` ).
* *column1*, *Column2*,...: (optional) Spaltennamen. Die Ausgabe Tabelle enthält eine zusätzliche Spalte für jede angegebene Spalte. Standard: alle Spalten außer der pivotierten Spalte und der Aggregations Spalte.

**Rückgabe**

Pivot gibt die gedrehte Tabelle mit angegebenen Spalten (*column1*, *Column2*,...) und allen eindeutigen Werten der Pivotspalten zurück. Jede Zelle für die pivotierten Spalten enthält die Berechnung der Aggregatfunktion.

**Hinweis**

Das Ausgabe Schema des `pivot` Plug-ins basiert auf den Daten, sodass die Abfrage möglicherweise ein anderes Schema für zwei Ausführungen erzeugt. Dies bedeutet auch, dass die Abfrage, die auf entpackte Spalten verweist, jederzeit "beschädigt" werden kann. Aus diesem Grund empfiehlt es sich nicht, dieses Plug-in für Automatisierungs Aufträge zu verwenden.

**Beispiele**

### <a name="pivot-by-a-column"></a>Pivotieren durch eine Spalte

Zählen Sie für jeden EventType und die Zustände, beginnend mit "Al", die Anzahl der Ereignisse dieses Typs in diesem Zustand.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| project State, EventType 
| where State startswith "AL" 
| where EventType has "Wind" 
| evaluate pivot(State)
```

|EventType|ALABAMA|Alaska|
|---|---|---|
|Sturm|352|1|
|Hoher Wind|0|95|
|Kalte Kälte/Wind Kühlung|0|10|
|Starker Wind|22|0|


### <a name="pivot-by-a-column-with-aggregation-function"></a>Pivotieren Sie nach einer Spalte mit einer Aggregations Funktion.

Zeigen Sie für jeden EventType und die Zustände, beginnend mit "ar", die Gesamtzahl der direkten Todesfälle an.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect))
```

|EventType|ARKANSAS|Arizona|
|---|---|---|
|Starker Regen|1|0|
|Sturm|1|0|
|Leichte|0|1|
|Überschwemmung|0|6|
|Starker Wind|1|0|
|Hitze|3|0|


### <a name="pivot-by-a-column-with-aggregation-function-and-a-single-additional-column"></a>Pivotieren Sie nach einer Spalte mit einer Aggregations Funktion und einer einzelnen zusätzlichen Spalte.

Das Ergebnis ist mit dem vorherigen Beispiel identisch.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType)
```

|EventType|ARKANSAS|Arizona|
|---|---|---|
|Starker Regen|1|0|
|Sturm|1|0|
|Leichte|0|1|
|Überschwemmung|0|6|
|Starker Wind|1|0|
|Hitze|3|0|


### <a name="specify-the-pivoted-column-aggregation-function-and-multiple-additional-columns"></a>Geben Sie die pivotierte Spalte, die Aggregations Funktion und mehrere zusätzliche Spalten an.

Addieren Sie für jeden Ereignistyp (Quelle und Zustand) die Anzahl der direkten Todesfälle.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType, Source)
```

|EventType|`Source`|ARKANSAS|Arizona|
|---|---|---|---|
|Starker Regen|Katastrophenschutz|1|0|
|Sturm|Katastrophenschutz|1|0|
|Leichte|Zeitung|0|1|
|Überschwemmung|Ausgebildeter „Spotter“|0|2|
|Überschwemmung|Broadcast Medien|0|3|
|Überschwemmung|Zeitung|0|1|
|Starker Wind|Strafverfolgungsbehörden|1|0|
|Hitze|Zeitung|3|0|
