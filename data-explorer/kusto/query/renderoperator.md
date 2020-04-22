---
title: Renderoperator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Renderoperator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/29/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 645860405a165f3304f655e42d2ced9b8777b8d0
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765474"
---
# <a name="render-operator"></a>render-Operator

Weist den Benutzer-Agent an, die Ergebnisse der Abfrage auf eine bestimmte Weise zu rendern.

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * Der Renderoperator sollte der letzte Operator in der Abfrage sein und nur bei Abfragen verwendet werden, die ein einzelnes tabellentakuläres Datenstromergebnis erzeugen.
> * Der Renderoperator ändert keine Daten. Es wird eine Anmerkung ("Visualisierung") in die erweiterten Eigenschaften des Ergebnisses eingefügt. Die Anmerkung enthält die Informationen, die der Operator in der Abfrage bereitgestellt hat.
> * Die Interpretation der Visualisierungsinformationen erfolgt durch den Benutzer-Agenten. Verschiedene Agenten (z. B. Kusto.Explorer,Kusto.WebExplorer) unterstützen möglicherweise unterschiedliche Visualisierungen.

**Syntax**

*T* `|` `render` *T-Visualisierung* `with` `(` [ *PropertyName* `=` *PropertyValue* [`,` ...] `)`]

Hierbei gilt:

* *Die Visualisierung* gibt die Art der zu verwendenden Visualisierung an. Die unterstützten Werte sind:

::: zone pivot="azuredataexplorer"

|*Visualisierung*     |BESCHREIBUNG|
|--------------------|-|
| `anomalychart`     | Ähnlich wie das Zeitdiagramm, hebt jedoch [Anomalien](./samples.md#get-more-out-of-your-data-in-kusto-using-machine-learning) mit [series_decompose_anomalies](./series-decompose-anomaliesfunction.md) Funktion hervor. |
| `areachart`        | Flächendiagramm. Die erste Spalte ist die x-Achse und sollte eine numerische Spalte sein. Andere numerische Spalten sind y-Achsen. |
| `barchart`         | Die erste Spalte ist die x-Achse und kann Text, Datumszeit oder numerisch sein. Andere Spalten sind numerisch und werden als horizontale Streifen angezeigt.|
| `card`             | Der erste Ergebnisdatensatz wird als Satz von Skalarwerten behandelt und als Karte angezeigt. |
| `columnchart`      | Wie `barchart` bei vertikalen Streifen statt horizontalen Streifen.|
| `ladderchart`      | Die letzten beiden Spalten sind die x-Achse, andere Spalten sind y-Achse.|
| `linechart`        | ein Liniendiagramm. Die erste Spalte ist x-achse und sollte eine numerische Spalte sein. Andere numerische Spalten sind y-Achsen. |
| `piechart`         | Die erste Spalte ist eine Farbachse, die zweite Spalte enthält Zahlen. |
| `pivotchart`       | Zeigt eine Pivot-Tabelle und ein Diagramm an. Der Benutzer kann Daten, Spalten, Zeilen und verschiedene Diagrammtypen interaktiv auswählen. |
| `scatterchart`     | Punktediagramm. Die erste Spalte ist x-achse und sollte eine numerische Spalte sein. Andere numerische Spalten sind y-Achsen. |
| `stackedareachart` | Gestapeltes Flächendiagramm. Die erste Spalte ist x-achse und sollte eine numerische Spalte sein. Andere numerische Spalten sind y-Achsen. |
| `table`            | Standard - Ergebnisse werden als Tabelle angezeigt.|
| `timechart`        | ein Liniendiagramm. Die erste Spalte ist die X-Achse und muss einen datetime-Wert enthalten. Andere (numerische) Spalten sind y-Achsen. Es gibt eine Zeichenfolgenspalte, deren Werte verwendet werden, um die numerischen Spalten zu "gruppieren" und verschiedene Linien im Diagramm zu erstellen (weitere Zeichenfolgenspalten werden ignoriert). |
| `timepivot`        | Interaktive Navigation über die Zeitlinie der Ereignisse (Pivoting auf der Zeitachse)|

::: zone-end

::: zone pivot="azuremonitor"

|*Visualisierung*     |BESCHREIBUNG|
|--------------------|-|
| `areachart`        | Flächendiagramm. Die erste Spalte ist die x-Achse und sollte eine numerische Spalte sein. Andere numerische Spalten sind y-Achsen. |
| `barchart`         | Die erste Spalte ist die x-Achse und kann Text, Datumszeit oder numerisch sein. Andere Spalten sind numerisch und werden als horizontale Streifen angezeigt.|
| `columnchart`      | Wie `barchart` bei vertikalen Streifen statt horizontalen Streifen.|
| `piechart`         | Die erste Spalte ist eine Farbachse, die zweite Spalte enthält Zahlen. |
| `scatterchart`     | Punktediagramm. Die erste Spalte ist die x-Achse und sollte eine numerische Spalte sein. Andere numerische Spalten sind y-Achsen. |
| `table`            | Standard - Ergebnisse werden als Tabelle angezeigt.|
| `timechart`        | ein Liniendiagramm. Die erste Spalte ist die X-Achse und muss einen datetime-Wert enthalten. Andere (numerische) Spalten sind y-Achsen. Es gibt eine Zeichenfolgenspalte, deren Werte verwendet werden, um die numerischen Spalten zu "gruppieren" und verschiedene Linien im Diagramm zu erstellen (weitere Zeichenfolgenspalten werden ignoriert).|

::: zone-end

* *PropertyName*/*PropertyValue* gibt zusätzliche Informationen an, die beim Rendern verwendet werden sollen.
  Alle Eigenschaften sind optional. Folgende Eigenschaften werden unterstützt:

::: zone pivot="azuredataexplorer"

|*Propertyname*|*Propertyvalue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |Gibt an, ob der Wert jeder Kennzahl zu allen Vorgängern addiert wird. (`true` `false`oder )|
|`kind`        |Weitere Ausarbeitung der Visualisierungsart. Siehe unten.                         |
|`legend`      |Gibt an, ob eine`visible` `hidden`Legende angezeigt werden soll ( oder ).                       |
|`series`      |Durch Kommas getrennte Liste von Spalten, deren kombinierte Werte pro Datensatz die Reihe definieren, zu der datensatzgehört wird.|
|`ymin`        |Der Mindestwert, der auf der Y-Achse angezeigt werden soll.                                      |
|`ymax`        |Der Maximalwert, der auf der Y-Achse angezeigt werden soll.                                      |
|`title`       |Der Titel der Visualisierung `string`(vom Typ ).                                |
|`xaxis`       |So skalieren Sie`linear` die `log`x-Achse ( oder ).                                      |
|`xcolumn`     |Welche Spalte im Ergebnis für die x-Achse verwendet wird.                                |
|`xtitle`      |Der Titel der x-Achse `string`(vom Typ ).                                       |
|`yaxis`       |So skalieren Sie`linear` die `log`y-Achse ( oder ).                                      |
|`ycolumns`    |Durch Kommas getrennte Liste von Spalten, die aus den werten pro Wert der x-Spalte bestehen.|
|`ysplit`      |So teilen Sie mehrere Visualisierungen auf. Siehe unten.                               |
|`ytitle`      |Der Titel der y-Achse `string`(vom Typ ).                                       |
|`anomalycolumns`|Eigenschaft, die `anomalychart`nur für relevant ist. Durch Kommas getrennte Liste von Spalten, die als Anomaliereihe betrachtet und als Punkte im Diagramm angezeigt werden|

::: zone-end

::: zone pivot="azuremonitor"

|*Propertyname*|*Propertyvalue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |Weitere Ausarbeitung der Visualisierungsart. Siehe unten.                         |
|`series`      |Durch Kommas getrennte Liste von Spalten, deren kombinierte Werte pro Datensatz die Reihe definieren, zu der datensatzgehört wird.|
|`title`       |Der Titel der Visualisierung `string`(vom Typ ).                                |
|`yaxis`       |So skalieren Sie`linear` die `log`y-Achse ( oder ).                                      |

::: zone-end

Einige Visualisierungen können durch die `kind` Bereitstellung der Eigenschaft weiter ausgearbeitet werden.
Diese lauten wie folgt:

|*Visualisierung*|`kind`             |BESCHREIBUNG                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |Jede "Fläche" steht für sich.     |
|               |`unstacked`        |Identisch mit `default`.                 |
|               |`stacked`          |Stapeln Sie "Bereiche" auf der rechten Seite.        |
|               |`stacked100`       |Stapeln Sie "Bereiche" auf der rechten Seite und strecken Sie jeden auf die gleiche Breite wie die anderen.|
|`barchart`     |`default`          |Jede "Bar" steht für sich.      |
|               |`unstacked`        |Identisch mit `default`.                 |
|               |`stacked`          |Stapeln Sie "Bars".                      |
|               |`stacked100`       |Stapeln Sie "bard" und dehnen Sie jedes auf die gleiche Breite wie die anderen.|
|`columnchart`  |`default`          |Jede "Säule" steht für sich.   |
|               |`unstacked`        |Identisch mit `default`.                 |
|               |`stacked`          |Stapeln Sie "Spalten" übereinander.|
|               |`stacked100`       |Stapeln Sie "Spalten" und dehnen Sie jede auf die gleiche Höhe wie die anderen.|
|`piechart`     |`map`              |Erwartete Spalten sind [Längengrad, Breitengrad] oder GeoJSON-Punkt, Farbachse und numerisch. Wird im Kusto Explorer-Desktop unterstützt.|
|`scatterchart` |`map`              |Erwartete Spalten sind [Längengrad, Breitengrad] oder GeoJSON-Punkt. Die Serienspalte ist optional. Wird im Kusto Explorer-Desktop unterstützt.|

::: zone pivot="azuredataexplorer"

Einige Visualisierungen unterstützen die Aufteilung in mehrere y-Achsenwerte:

|`ysplit`  |BESCHREIBUNG                                                       |
|----------|------------------------------------------------------------------|
|`none`    |Für alle Seriendaten wird eine einzelne y-Achse angezeigt. (Standardwert)       |
|`axes`    |Ein einzelnes Diagramm wird mit mehreren y-Achsen (eine pro Reihe) angezeigt.|
|`panels`  |Für jeden Wert wird `ycolumn` ein Diagramm gerendert (bis zu einem gewissen Grenzwert).|

::: zone-end

> [!NOTE]
> Das Datenmodell des Renderoperators betrachtet die Tabellendaten so, als hätte er drei Arten von Spalten:
>
> * Die x-Achsenspalte (angezeigt durch die `xcolumn` Eigenschaft).
> * Die Serienspalten (eine beliebige Anzahl `series` von Spalten, die von der Eigenschaft angegeben sind.) Für jeden Datensatz definieren die kombinierten Werte dieser Spalten eine einzelne Reihe, und das Diagramm hat so viele Reihen, wie es unterschiedliche kombinierte Werte gibt.
> * Die y-Achsenspalten (eine beliebige `ycolumns` Anzahl von Spalten, die durch die Eigenschaft angegeben werden).
  Für jeden Datensatz weist die Reihe so viele Messungen ("Punkte" im Diagramm) auf, wie y-Achsenspalten vorhanden sind.

> [!TIP]
> 
> * Verwenden `where` `summarize` Sie `top` , und begrenzen Sie die angezeigte Lautstärke.
> * Sortieren Sie die Daten, um die Reihenfolge der x-Achse zu definieren.
> * Benutzer-Agents können den Wert von Eigenschaften, die nicht von der Abfrage angegeben werden, "erraten". Insbesondere mit "uninteressanten" Spalten im Schema des Ergebnisses könnte in ihnen zu raten falsch übersetzen. Versuchen Sie, solche Spalten zu projizieren, wenn dies geschieht. 

**Beispiel**

```kusto
range x from -2 to 2 step 0.1
| extend sin = sin(x), cos = cos(x)
| extend x_sign = iif(x > 0, "x_pos", "x_neg")
| extend sum_sign = iif(sin + cos > 0, "sum_pos", "sum_neg")
| render linechart with  (ycolumns = sin, cos, series = x_sign, sum_sign)
```

::: zone pivot="azuredataexplorer"

[Renderbeispiele im Tutorial](./tutorial.md#render-display-a-chart-or-table).

[Anomalieerkennung](./samples.md#get-more-out-of-your-data-in-kusto-using-machine-learning)

::: zone-end
