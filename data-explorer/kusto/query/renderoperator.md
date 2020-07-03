---
title: 'Rendering-Operator: Azure Daten-Explorer'
description: In diesem Artikel wird der Rendering-Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/29/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 90b2a08d4fdde6ab6a74b12632c9310029f8fc1b
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85902105"
---
# <a name="render-operator"></a>render-Operator

Weist den Benutzer-Agent an, die Ergebnisse der Abfrage auf eine bestimmte Weise zu erzeugen.

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * Der Rendering-Operator muss der letzte Operator in der Abfrage sein und wird nur mit Abfragen verwendet, die ein einzelnes Tabular Data Stream Ergebnis erzeugen.
> * Der Rendering-Operator ändert keine Daten. Sie fügt eine Anmerkung ("Visualisierung") in die erweiterten Eigenschaften des Ergebnisses ein. Die-Anmerkung enthält die Informationen, die vom-Operator in der Abfrage bereitgestellt werden.
> * Die Interpretation der Visualisierungs Informationen erfolgt über den Benutzer-Agent. Verschiedene Agents (z. b. Kusto. Explorer, Kusto. Webexplorer) unterstützen möglicherweise unterschiedliche Visualisierungen.

**Syntax**

*T* - `|` `render` *Visualisierung* [ `with` `(` *propertyName* `=` *PropertyValue* [ `,` ...] `)` ]

Hierbei gilt:

* *Visualisierung* gibt die Art der zu verwendenden Visualisierung an. Die unterstützten Werte sind:

::: zone pivot="azuredataexplorer"

|*Visualisierung*     |Beschreibung|
|--------------------|-|
| `anomalychart`     | Vergleichbar mit timechart, [hebt jedoch Anomalien](./samples.md#get-more-out-of-your-data-in-kusto-with-machine-learning) mithilfe [series_decompose_anomalies](./series-decompose-anomaliesfunction.md) Funktion hervor. |
| `areachart`        | Flächen Diagramm. Die erste Spalte ist die x-Achse und sollte eine numerische Spalte sein. Andere numerische Spalten sind y-Achsen. |
| `barchart`         | Die erste Spalte ist die x-Achse und kann "Text", "DateTime" oder "numeric" sein. Andere Spalten sind numerisch und werden als horizontale Striche angezeigt.|
| `card`             | Der erste Ergebnisdaten Satz wird als Satz von skalaren Werten behandelt und als Karte angezeigt. |
| `columnchart`      | Wie `barchart` bei vertikalen Streifen anstelle von horizontalen Bändern.|
| `ladderchart`      | Die letzten zwei Spalten sind die x-Achse, andere Spalten sind y-Achse.|
| `linechart`        | ein Liniendiagramm. Die erste Spalte ist die x-Achse und sollte eine numerische Spalte sein. Andere numerische Spalten sind y-Achsen. |
| `piechart`         | Die erste Spalte ist eine Farbachse, die zweite Spalte enthält Zahlen. |
| `pivotchart`       | Zeigt eine Pivottabelle und ein Diagramm an. Der Benutzer kann Daten, Spalten, Zeilen und verschiedene Diagrammtypen interaktiv auswählen. |
| `scatterchart`     | Punkt Diagramm. Die erste Spalte ist die x-Achse und sollte eine numerische Spalte sein. Andere numerische Spalten sind y-Achsen. |
| `stackedareachart` | Gestapeltes Flächen Diagramm. Die erste Spalte ist die x-Achse und sollte eine numerische Spalte sein. Andere numerische Spalten sind y-Achsen. |
| `table`            | Standard-Ergebnisse werden als Tabelle angezeigt.|
| `timechart`        | ein Liniendiagramm. Die erste Spalte ist die X-Achse und muss einen datetime-Wert enthalten. Andere (numerische) Spalten sind y-Achsen. Es gibt eine Zeichen folgen Spalte, deren Werte verwendet werden, um die numerischen Spalten zu gruppieren und verschiedene Zeilen im Diagramm zu erstellen (weitere Zeichen folgen Spalten werden ignoriert). |
| `timepivot`        | Interaktive Navigation über die Ereignis Zeitachse (Pivotierung auf der Zeitachse)|

::: zone-end

::: zone pivot="azuremonitor"

|*Visualisierung*     |Beschreibung|
|--------------------|-|
| `areachart`        | Flächen Diagramm. Die erste Spalte ist die x-Achse und sollte eine numerische Spalte sein. Andere numerische Spalten sind y-Achsen. |
| `barchart`         | Die erste Spalte ist die x-Achse und kann "Text", "DateTime" oder "numeric" sein. Andere Spalten sind numerisch und werden als horizontale Striche angezeigt.|
| `columnchart`      | Wie `barchart` bei vertikalen Streifen anstelle von horizontalen Bändern.|
| `piechart`         | Die erste Spalte ist eine Farbachse, die zweite Spalte enthält Zahlen. |
| `scatterchart`     | Punkt Diagramm. Die erste Spalte ist die x-Achse und sollte eine numerische Spalte sein. Andere numerische Spalten sind y-Achsen. |
| `table`            | Standard-Ergebnisse werden als Tabelle angezeigt.|
| `timechart`        | ein Liniendiagramm. Die erste Spalte ist die X-Achse und muss einen datetime-Wert enthalten. Andere (numerische) Spalten sind y-Achsen. Es gibt eine Zeichen folgen Spalte, deren Werte verwendet werden, um die numerischen Spalten zu gruppieren und verschiedene Zeilen im Diagramm zu erstellen (weitere Zeichen folgen Spalten werden ignoriert).|

::: zone-end

* *PropertyName* / *PropertyValue* gibt zusätzliche Informationen an, die beim Rendern verwendet werden.
  Alle Eigenschaften sind optional. Folgende Eigenschaften werden unterstützt:

::: zone pivot="azuredataexplorer"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |Gibt an, ob der Wert jedes Measures allen seinen Vorgängern hinzugefügt wird. ( `true` oder `false` )|
|`kind`        |Weitere Erläuterung der Visualisierungs Art. Siehe unten.                         |
|`legend`      |Gibt an, ob eine Legende oder nicht angezeigt werden soll ( `visible` oder `hidden` ).                       |
|`series`      |Durch Trennzeichen getrennte Liste von Spalten, deren kombinierte Werte pro Datensatz die Reihen definieren, zu denen der Datensatz gehört.|
|`ymin`        |Der Minimalwert, der auf der Y-Achse angezeigt werden soll.                                      |
|`ymax`        |Der maximale Wert, der auf der Y-Achse angezeigt werden soll.                                      |
|`title`       |Der Titel der Visualisierung (vom Typ `string` ).                                |
|`xaxis`       |Skalieren der x-Achse ( `linear` oder `log` ).                                      |
|`xcolumn`     |Welche Spalte im Ergebnis für die x-Achse verwendet wird.                                |
|`xtitle`      |Der Titel der x-Achse (vom Typ `string` ).                                       |
|`yaxis`       |Skalieren der y-Achse ( `linear` oder `log` ).                                      |
|`ycolumns`    |Durch Trennzeichen getrennte Liste von Spalten, die aus den Werten, die pro Wert der x-Spalte bereitgestellt werden, bestehen.|
|`ysplit`      |Vorgehensweise beim Aufteilen mehrerer Visualisierungen Siehe unten.                               |
|`ytitle`      |Der Titel der y-Achse (vom Typ `string` ).                                       |
|`anomalycolumns`|Eigenschaft, die nur für relevant ist `anomalychart` . Durch Trennzeichen getrennte Liste von Spalten, die als anomaliereihe angesehen und als Punkte im Diagramm angezeigt werden.|

::: zone-end

::: zone pivot="azuremonitor"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |Weitere Erläuterung der Visualisierungs Art. Siehe unten.                         |
|`series`      |Durch Trennzeichen getrennte Liste von Spalten, deren kombinierte Werte pro Datensatz die Reihen definieren, zu denen der Datensatz gehört.|
|`title`       |Der Titel der Visualisierung (vom Typ `string` ).                                |
|`yaxis`       |Skalieren der y-Achse ( `linear` oder `log` ).                                      |

::: zone-end

Einige Visualisierungen können durch Bereitstellen der-Eigenschaft weiterentwickelt werden `kind` .
Diese lauten wie folgt:

|*Visualisierung*|`kind`             |Beschreibung                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |Jeder "Bereich" steht eigenständig.     |
|               |`unstacked`        |Wie in `default`.                 |
|               |`stacked`          |Stapeln Sie die Bereiche auf der rechten Seite.        |
|               |`stacked100`       |Stapeln Sie die Bereiche auf der rechten Seite, und Strecken Sie diese auf die gleiche Breite wie die anderen.|
|`barchart`     |`default`          |Jede "Leiste" steht alleine.      |
|               |`unstacked`        |Wie in `default`.                 |
|               |`stacked`          |Stapel "leisten".                      |
|               |`stacked100`       |Stapeln Sie "Bard", und Strecken Sie die einzelnen in dieselbe Breite wie die anderen.|
|`columnchart`  |`default`          |Jede "Spalte" steht eigenständig.   |
|               |`unstacked`        |Wie in `default`.                 |
|               |`stacked`          |Stapeln Sie die Spalten in der anderen.|
|               |`stacked100`       |Stapeln Sie "Columns" (Spalten), und Strecken Sie diese auf dieselbe Höhe wie die anderen.|
|`piechart`     |`map`              |Erwartete Spalten sind [Längengrad, Breitengrad], geojson-Punkt, farbachse und numerisch. Wird in Kusto Explorer Desktop unterstützt.|
|`scatterchart` |`map`              |Erwartete Spalten sind [Längengrad, Breitengrad] oder geojson-Punkt. Die Reihen Spalte ist optional. Wird in Kusto Explorer Desktop unterstützt.|

::: zone pivot="azuredataexplorer"

Einige Visualisierungen unterstützen das Aufteilen in mehrere y-Achsen Werte:

|`ysplit`  |Beschreibung                                                       |
|----------|------------------------------------------------------------------|
|`none`    |Für alle Reihen Daten wird eine einzelne y-Achse angezeigt. (Standardwert)       |
|`axes`    |Es wird ein einzelnes Diagramm mit mehreren y-Achsen angezeigt (eine pro Reihe).|
|`panels`  |Ein Diagramm wird für jeden `ycolumn` Wert (bis zu einem Grenzwert) gerendert.|

::: zone-end

> [!NOTE]
> Das Datenmodell des Rendering-Operators prüft die tabellarischen Daten so, als wären es drei Arten von Spalten:
>
> * Die Spalte der x-Achse (angegeben durch die- `xcolumn` Eigenschaft).
> * Die Reihen Spalten (eine beliebige Anzahl von Spalten, die von der-Eigenschaft angegeben werden `series` .) Für jeden Datensatz definieren die kombinierten Werte dieser Spalten eine einzelne Reihe, und das Diagramm verfügt über so viele Reihen, wie es unterschiedliche kombinierte Werte gibt.
> * Die Spalten der y-Achse (eine beliebige Anzahl von Spalten, die von der-Eigenschaft angegeben werden `ycolumns` ).
  Für jeden Datensatz enthält die Reihe so viele Messungen ("Punkte" im Diagramm), wie die Spalten der y-Achse vorhanden sind.

> [!TIP]
> 
> * Verwenden `where` `summarize` Sie und, um das angezeigter `top` Volume einzuschränken.
> * Sortieren Sie die Daten, um die Reihenfolge der x-Achse zu definieren.
> * Benutzer-Agents können den Wert von Eigenschaften "erraten", die nicht durch die Abfrage angegeben werden. Insbesondere kann es vorkommen, dass sich "uninteressante" Spalten im Schema des Ergebnisses in ein falsches Problem setzen. Versuchen Sie, solche Spalten zu projizieren, wenn dies geschieht. 

**Beispiel**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from -2 to 2 step 0.1
| extend sin = sin(x), cos = cos(x)
| extend x_sign = iif(x > 0, "x_pos", "x_neg")
| extend sum_sign = iif(sin + cos > 0, "sum_pos", "sum_neg")
| render linechart with  (ycolumns = sin, cos, series = x_sign, sum_sign)
```

::: zone pivot="azuredataexplorer"

[Renderingbeispiele in diesem Tutorial](./tutorial.md#render-display-a-chart-or-table).

[Anomalieerkennung](./samples.md#get-more-out-of-your-data-in-kusto-with-machine-learning)

::: zone-end
