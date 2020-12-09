---
title: 'render-Operator: Azure Data Explorer'
description: In diesem Artikel wird der render-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/08/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8370e69914b2bc5e141321a6bc6722bba6f1fd4d
ms.sourcegitcommit: 79d923d7b7e8370726974e67a984183905f323ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96868602"
---
# <a name="render-operator"></a>render-Operator

Weist den Benutzer-Agent an, die Ergebnisse der Abfrage auf eine bestimmte Weise zu rendern.

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * Der render-Operator muss der letzte Operator in der Abfrage sein und wird nur mit Abfragen verwendet, die ein einzelnes Tabular Data Stream-Ergebnis generieren.
> * Der render-Operator ändert keine Daten. Sie fügt eine Anmerkung („Visualisierung“) in die erweiterten Eigenschaften des Ergebnisses ein. Die Anmerkung enthält die Informationen, die vom Operator in der Abfrage bereitgestellt werden.
> * Die Interpretation der Visualisierungsinformationen erfolgt über den Benutzer-Agent. Verschiedene Agents (z. B. Kusto.Explorer, Kusto.WebExplorer) unterstützen möglicherweise unterschiedliche Visualisierungen.

## <a name="syntax"></a>Syntax

*T* `|` `render` *Visualization* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`]

Hierbei gilt:

* *Visualization* gibt die Art der zu verwendenden Visualisierung an. Die unterstützten Werte sind:

::: zone pivot="azuredataexplorer"

|*Visualisierung*     |BESCHREIBUNG|
|--------------------|-|
| `anomalychart`     | Ähnlich wie timechart, [hebt Anomalien](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning) jedoch mit der [series_decompose_anomalies](./series-decompose-anomaliesfunction.md)-Funktion hervor. |
| `areachart`        | Bereichsdiagramm. Die erste Spalte ist die x-Achse und muss eine numerische Spalte sein. Andere numerische Spalten werden als y-Achsen verwendet. |
| `barchart`         | Die erste Spalte ist die x-Achse und kann Text, DateTime oder numerische Daten enthalten. Andere Spalten sind numerisch und werden als horizontale Streifen angezeigt.|
| `card`             | Der erste Ergebnisdatensatz wird als Satz von Skalarwerten behandelt und als Karte angezeigt. |
| `columnchart`      | Wie `barchart` mit vertikalen Streifen anstelle von horizontalen Streifen.|
| `ladderchart`      | Die letzten zwei Spalten sind die x-Achse, andere Spalten sind die y-Achse.|
| `linechart`        | ein Liniendiagramm. Die erste Spalte ist die x-Achse und muss eine numerische Spalte sein. Andere numerische Spalten werden als y-Achsen verwendet. |
| `piechart`         | Die erste Spalte ist eine Farbachse, die zweite Spalte enthält Zahlen. |
| `pivotchart`       | Zeigt eine Pivottabelle und ein Diagramm an. Der Benutzer kann Daten, Spalten, Zeilen und verschiedene Diagrammtypen interaktiv auswählen. |
| `scatterchart`     | Punktdiagramm. Die erste Spalte ist die x-Achse und muss eine numerische Spalte sein. Andere numerische Spalten werden als y-Achsen verwendet. |
| `stackedareachart` | Gestapeltes Flächendiagramm. Die erste Spalte ist die x-Achse und muss eine numerische Spalte sein. Andere numerische Spalten werden als y-Achsen verwendet. |
| `table`            | Ergebnisse werden standardmäßig als Tabelle angezeigt.|
| `timechart`        | ein Liniendiagramm. Die erste Spalte ist die X-Achse und muss einen datetime-Wert enthalten. Andere (numerische) Spalten werden als y-Achsen verwendet. Es gibt eine Zeichenfolgenspalte, deren Werte verwendet werden, um die numerischen Spalten zu „gruppieren“ und verschiedene Linien im Diagramm zu erstellen (weitere Zeichenfolgenspalten werden ignoriert). |
| `timepivot`        | Interaktive Navigation durch die Ereignisse der Zeitachse (Pivotierung auf der Zeitachse).|

::: zone-end

::: zone pivot="azuremonitor"

|*Visualisierung*     |BESCHREIBUNG|
|--------------------|-|
| `areachart`        | Bereichsdiagramm. Die erste Spalte ist die x-Achse und muss eine numerische Spalte sein. Andere numerische Spalten werden als y-Achsen verwendet. |
| `barchart`         | Die erste Spalte ist die x-Achse und kann Text, DateTime oder numerische Daten enthalten. Andere Spalten sind numerisch und werden als horizontale Streifen angezeigt.|
| `columnchart`      | Wie `barchart` mit vertikalen Streifen anstelle von horizontalen Streifen.|
| `piechart`         | Die erste Spalte ist eine Farbachse, die zweite Spalte enthält Zahlen. |
| `scatterchart`     | Punktdiagramm. Die erste Spalte ist die x-Achse und muss eine numerische Spalte sein. Andere numerische Spalten werden als y-Achsen verwendet. |
| `table`            | Ergebnisse werden standardmäßig als Tabelle angezeigt.|
| `timechart`        | ein Liniendiagramm. Die erste Spalte ist die X-Achse und muss einen datetime-Wert enthalten. Andere (numerische) Spalten werden als y-Achsen verwendet. Es gibt eine Zeichenfolgenspalte, deren Werte verwendet werden, um die numerischen Spalten zu „gruppieren“ und verschiedene Linien im Diagramm zu erstellen (weitere Zeichenfolgenspalten werden ignoriert).|

::: zone-end

* *PropertyName*/*PropertyValue* zusätzliche Informationen an, die beim Rendern verwendet werden sollen.
  Alle Eigenschaften sind optional. Folgende Eigenschaften werden unterstützt:

::: zone pivot="azuredataexplorer"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |Gibt an, ob der Wert jedes Measures allen seinen Vorgängern hinzugefügt wird. (`true` oder `false`)|
|`kind`        |Weitere Erläuterung der Visualisierungsart. Siehe unten.                         |
|`legend`      |Gibt an, ob eine Legende angezeigt werden soll (`visible` oder `hidden`).                       |
|`series`      |Durch Trennzeichen getrennte Liste von Spalten, deren kombinierte Werte pro Datensatz die Reihen definieren, zu denen der Datensatz gehört.|
|`ymin`        |Der Minimalwert, der auf der Y-Achse angezeigt werden soll.                                      |
|`ymax`        |Der Maximalwert, der auf der Y-Achse angezeigt werden soll.                                      |
|`title`       |Der Titel der Visualisierung (vom Typ `string`).                                |
|`xaxis`       |Skalierungsart der x-Achse (`linear` oder `log`).                                      |
|`xcolumn`     |Gibt an, welche Spalte im Ergebnis für die x-Achse verwendet wird.                                |
|`xtitle`      |Der Titel der x-Achse (vom Typ `string`).                                       |
|`yaxis`       |Skalierungsart der y-Achse (`linear` oder `log`).                                      |
|`ycolumns`    |Durch Trennzeichen getrennte Liste von Spalten, die aus den Werten bestehen, die pro Wert der x-Spalte bereitgestellt werden.|
|`ysplit`      |Vorgehensweise beim Aufteilen mehrerer Visualisierungen Siehe unten.                               |
|`ytitle`      |Der Titel der y-Achse (vom Typ `string`).                                       |
|`anomalycolumns`|Eigenschaft, die nur für `anomalychart` relevant ist. Durch Trennzeichen getrennte Liste von Spalten, die als Anomaliereihe angesehen und als Punkte im Diagramm angezeigt werden.|

::: zone-end

::: zone pivot="azuremonitor"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |Weitere Erläuterung der Visualisierungsart. Siehe unten.                         |
|`series`      |Durch Trennzeichen getrennte Liste von Spalten, deren kombinierte Werte pro Datensatz die Reihen definieren, zu denen der Datensatz gehört.|
|`title`       |Der Titel der Visualisierung (vom Typ `string`).                                |
|`yaxis`       |Skalierungsart der y-Achse (`linear` oder `log`).                                      |

::: zone-end

Einige Visualisierungen können durch Bereitstellen der `kind`-Eigenschaft weiterentwickelt werden.
Diese lauten wie folgt:

|*Visualisierung*|`kind`             |BESCHREIBUNG                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |Jeder „Bereich“ steht für sich selbst.     |
|               |`unstacked`        |Wie in `default`.                 |
|               |`stacked`          |Stapelt „Bereiche“ auf der rechten Seite.        |
|               |`stacked100`       |Stapelt „Bereiche“ auf der rechten Seite und streckt diese auf die gleiche Breite wie die anderen.|
|`barchart`     |`default`          |Jeder „Balken“ steht für sich selbst.      |
|               |`unstacked`        |Wie in `default`.                 |
|               |`stacked`          |Stapelt „Balken“.                      |
|               |`stacked100`       |Stapelt „Balken“ und streckt diese auf die gleiche Breite wie die anderen.|
|`columnchart`  |`default`          |Jede „Spalte“ steht für sich selbst.   |
|               |`unstacked`        |Wie in `default`.                 |
|               |`stacked`          |Stapelt „Spalten“ aufeinander.|
|               |`stacked100`       |Stapelt „Spalten“ und streckt diese auf die gleiche Höhe wie die anderen.|
|`scatterchart` |`map`              |Erwartete Spalten sind [Longitude, Latitude] oder „GeoJSON point“. Die Spalte „Series“ ist optional.|
|`piechart`     |`map`              |Erwartete Spalten sind [Longitude, Latitude] oder „GeoJSON point“, color-axis und „numeric“. Wird in Kusto Explorer-Desktop unterstützt.|

::: zone pivot="azuredataexplorer"

Einige Visualisierungen unterstützen das Aufteilen in mehrere Werte der y-Achse:

|`ysplit`  |BESCHREIBUNG                                                       |
|----------|------------------------------------------------------------------|
|`none`    |Für alle Reihendaten wird eine einzelne y-Achse angezeigt. (Standardwert)       |
|`axes`    |Es wird ein einzelnes Diagramm mit mehreren y-Achsen angezeigt (eine Achse pro Reihe).|
|`panels`  |Ein Diagramm wird für jeden `ycolumn`-Wert (bis zu einem Grenzwert) gerendert.|

> [!NOTE]
> Das Datenmodell des render-Operators prüft die tabellarischen Daten so, als wären es drei Arten von Spalten vorhanden:
>
> * Die Spalte der x-Achse (angegeben durch die `xcolumn`-Eigenschaft).
> * Die Reihenspalten (eine beliebige Anzahl von Spalten, die durch die `series`-Eigenschaft angegeben werden). Für jeden Datensatz definieren die kombinierten Werte dieser Spalten eine einzelne Reihe, und das Diagramm verfügt über so viele Reihen, wie es unterschiedliche kombinierte Werte gibt.
> * Die Spalten der y-Achse (eine beliebige Anzahl von Spalten, die durch die `ycolumns`-Eigenschaft angegeben werden).
  Für jeden Datensatz enthält die Reihe so viele Messungen („Punkte“ im Diagramm), wie Spalten der y-Achse vorhanden sind.

> [!TIP]
> 
> * Verwenden Sie `where`, `summarize` und `top`, um das angezeigter Volumen einzuschränken.
> * Sortieren Sie die Daten, um die Reihenfolge der x-Achse zu definieren.
> * Benutzer-Agents können den Wert von Eigenschaften „erraten“, die nicht durch die Abfrage angegeben werden. Insbesondere kann es vorkommen, dass „uninteressante“ Spalten im Schema des Ergebnisses dazu führen, dass sie falsch raten. Versuchen Sie, solche Spalten wegzuprojizieren, wenn dies geschieht. 

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from -2 to 2 step 0.1
| extend sin = sin(x), cos = cos(x)
| extend x_sign = iif(x > 0, "x_pos", "x_neg")
| extend sum_sign = iif(sin + cos > 0, "sum_pos", "sum_neg")
| render linechart with  (ycolumns = sin, cos, series = x_sign, sum_sign)
```

[Renderingbeispiele im Tutorial](./tutorial.md#displaychartortable)

[Anomalieerkennung](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning)

::: zone-end

::: zone pivot="azuremonitor"

> [!NOTE]
> Das Datenmodell des render-Operators prüft die tabellarischen Daten so, als wären es drei Arten von Spalten vorhanden:
>
> * Die Spalte der x-Achse (angegeben durch die `xcolumn`-Eigenschaft).
> * Die Reihenspalten (eine beliebige Anzahl von Spalten, die durch die `series`-Eigenschaft angegeben werden).
> * Die Spalten der y-Achse (eine beliebige Anzahl von Spalten, die durch die `ycolumns`-Eigenschaft angegeben werden).
  Für jeden Datensatz enthält die Reihe so viele Messungen („Punkte“ im Diagramm), wie Spalten der y-Achse vorhanden sind.

> [!TIP]
> 
> * Verwenden Sie `where`, `summarize` und `top`, um das angezeigter Volumen einzuschränken.
> * Sortieren Sie die Daten, um die Reihenfolge der x-Achse zu definieren.
> * Benutzer-Agents können den Wert von Eigenschaften „erraten“, die nicht durch die Abfrage angegeben werden. Insbesondere kann es vorkommen, dass „uninteressante“ Spalten im Schema des Ergebnisses dazu führen, dass sie falsch raten. Versuchen Sie, solche Spalten wegzuprojizieren, wenn dies geschieht. 

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

[Renderingbeispiele im Tutorial](./tutorial.md?pivots=azuremonitor#display-a-chart-or-table-render-1)


::: zone-end
