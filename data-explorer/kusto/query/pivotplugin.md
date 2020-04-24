---
title: Pivot-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird das Pivot-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e4ec5a94483ade822280ee4a71106c214bb4b9a5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511100"
---
# <a name="pivot-plugin"></a>Pivot-Plugin

Dreht eine Tabelle, indem die eindeutigen Werte aus einer Spalte in der Eingabetabelle in mehrere Spalten in der Ausgabetabelle umgewandelt werden, und führt Aggregationen aus, bei denen sie für alle verbleibenden Spaltenwerte erforderlich sind, die in der endgültigen Ausgabe gewünscht werden.

```kusto
T | evaluate pivot(PivotColumn)
```

**Syntax**

`T | evaluate pivot(`*pivotColumn*`[, `*aggregationFunktionsspalte1*`] [,`*column1* `[,` *Spalte2* ...`]])`

**Argumente**

* *pivotColumn*: Die zu drehende Spalte. Jeder eindeutige Wert aus dieser Spalte ist eine Spalte in der Ausgabetabelle.
* *Aggregationsfunktion*: (optional) aggregiert mehrere Zeilen in der Eingabetabelle zu einer einzelnen Zeile in der Ausgabetabelle. Derzeit unterstützte `min()`Funktionen: `any()` `sum()`, `dcount()` `max()`, `variance()`, `count()` , , `count()` `avg()`, `stdev()`, und (Standard ist ).
* *column1*, *column2*, ...: (optional) Spaltennamen. Die Ausgabetabelle enthält eine zusätzliche Spalte pro angegebener Spalte. Standard: alle Spalten außer der pivotierten Spalte und der Aggregationsspalte.

**Rückgabe**

Pivot gibt die gedrehte Tabelle mit den angegebenen Spalten (*Spalte1*, *Spalte2*, ...) sowie alle eindeutigen Werte der Pivot-Spalten zurück. Jede Zelle für die pivotierten Spalten enthält die Aggregatfunktionsberechnung.

**Hinweis**

Das Ausgabeschema `pivot` des Plugins basiert auf den Daten und daher kann die Abfrage unterschiedliche Schemas für zwei beliebige Durchläufe erzeugen. Dies bedeutet auch, dass Abfragen, die auf unverpackte Spalten verweisen, jederzeit "gebrochen" werden können. Aus diesem Grund - es wird nicht empfohlen, dieses Plugin für Automatisierungsaufträge zu verwenden.

**Beispiele**

### <a name="pivot-by-a-column"></a>Pivot durch eine Spalte

Zählen Sie für jeden EventType und die Status, die mit 'AL' beginnen, die Anzahl der Ereignisse dieses Typs in diesem Status.

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
|Extreme Kälte/Wind Chill|0|10|
|Starker Wind|22|0|


### <a name="pivot-by-a-column-with-aggregation-function"></a>Pivot durch eine Spalte mit Aggregationsfunktion.

Zeigen Sie für jeden EventType und die Zustände, die mit 'AR' beginnen, die Gesamtzahl der direkten Todesfälle an.

```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect))
```

|EventType|Arkansas|Arizona|
|---|---|---|
|Heavy Rain|1|0|
|Sturm|1|0|
|Blitz|0|1|
|Überschwemmung|0|6|
|Starker Wind|1|0|
|Hitze|3|0|


### <a name="pivot-by-a-column-with-aggregation-function-and-a-single-additional-column"></a>Pivot durch eine Spalte mit Aggregationsfunktion und einer einzelnen zusätzlichen Spalte.

Das Ergebnis ist identisch mit dem vorherigen Beispiel.

```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType)
```

|EventType|Arkansas|Arizona|
|---|---|---|
|Heavy Rain|1|0|
|Sturm|1|0|
|Blitz|0|1|
|Überschwemmung|0|6|
|Starker Wind|1|0|
|Hitze|3|0|


### <a name="specify-the-pivoted-column-aggregation-function-and-multiple-additional-columns"></a>Geben Sie die Pivoted-Spalte, die Aggregationsfunktion und mehrere zusätzliche Spalten an.

Summieren Sie für jeden Ereignistyp, jede Quelle und jeden Zustand die Anzahl der direkten Todesfälle.

```kusto
StormEvents 
| where State startswith "AR" 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType, Source)
```

|EventType|`Source`|Arkansas|Arizona|
|---|---|---|---|
|Heavy Rain|Katastrophenschutz|1|0|
|Sturm|Katastrophenschutz|1|0|
|Blitz|Zeitung|0|1|
|Überschwemmung|Ausgebildeter „Spotter“|0|2|
|Überschwemmung|Broadcast-Medien|0|3|
|Überschwemmung|Zeitung|0|1|
|Starker Wind|Strafverfolgungsbehörden|1|0|
|Hitze|Zeitung|3|0|