---
title: series_decompose_anomalies ()-Azure Daten-Explorer
description: In diesem Artikel wird series_decompose_anomalies () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: ded1f7ed499d0a8379fdf5b8e9949fa06351fc07
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250192"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

Die Anomalieerkennung basiert auf der Reihen Zerlegung.
Weitere Informationen finden Sie unter [series_decompose ()](series-decomposefunction.md).

Die Funktion nimmt einen Ausdruck, der eine Reihe (dynamisches numerisches Array) enthält, als Eingabe an und extrahiert anormale Punkte mit Bewertungen.

## <a name="syntax"></a>Syntax

`series_decompose_anomalies (`*Reihe* `[, ` *Schwellenwert* `,` *Saisonalität* `,` *Trend* `, ` *Test_points* `, ` *AD_method* `,` *Seasonality_threshold*`])`

## <a name="arguments"></a>Argumente

* *Series*: dynamische Array Zelle, bei der es sich um ein Array numerischer Werte handelt, normalerweise die resultierende Ausgabe von [make-Series-](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md) -Operatoren
* *Schwellen*Wert: anomalieschwellen Wert, Standardwert 1,5 (k Wert) zum Erkennen von milden oder stärkeren Anomalien
* *Saison*Abhängigkeit: eine ganze Zahl, die die saisonale Analyse steuert, die Folgendes enthält:
    * -1: automatische Erkennung der Saisonalität (mit [series_periods_detect](series-periods-detectfunction.md)) [Standard]
    * 0: keine Saisonalität (d. h. das Extrahieren dieser Komponente wird übersprungen)
    * Period: eine positive ganze Zahl, die den erwarteten Zeitraum in der Anzahl der Containereinheiten angibt. Wenn sich die Reihe z. b. in einem Stunde-Behälter befindet, beträgt die wöchentliche Zeit 168 Behälter.
* *Trend*: eine Zeichenfolge, die die Trend Analyse steuert, die Folgendes enthält:
    * "AVG": Trend Komponente als Mittelwert der Reihe definieren [Standard]
    * "None": kein Trend, Extrahieren dieser Komponente überspringen
    * "Linefit": Trend Komponente mithilfe von linearer Regression extrahieren
* *Test_points*: 0 [Standard] oder eine positive ganze Zahl, die die Anzahl der Punkte am Ende der Reihe angibt, die vom Lern Vorgang (Regression) ausgeschlossen werden sollen. Dieser Parameter sollte für Vorhersagezwecke festgelegt werden.
* *AD_method*: eine Zeichenfolge, die die anomalieerkennungsmethode für die restliche Zeitreihe steuert, die Folgendes enthält:
    * "ctukey": [Tukey-Fence-Test](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) mit benutzerdefiniertem 10.90. Perzentil-Bereich [Standard]
    * "Tukey": [der Fence Test von Tukey mit dem](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) standardmäßigen 25.75. Perzentil-Bereich. Weitere Informationen zu verbleibenden Zeitreihen finden Sie unter [series_outliers](series-outliersfunction.md)
* *Seasonality_threshold*: der Schwellenwert für die saisonalitätsbewertung, wenn *Saison alität* auf Autodetect festgelegt ist. Der Standardwert für das Ergebnis ist `0.6` . Weitere Informationen finden Sie unter [series_periods_detect](series-periods-detectfunction.md)

## <a name="returns"></a>Rückgabe

 Die-Funktion gibt die folgende Reihe zurück:

* `ad_flag`: Eine ternäre Reihe mit (+ 1,-1, 0), die eine Markierung nach oben/unten/keine Anomalie enthält
* `ad_score`: Anomaliebewertung
* `baseline`: Der vorhergesagte Wert der Reihe gemäß der Zerlegung

## <a name="the-algorithm"></a>Der Algorithmus

Diese Funktion folgt den folgenden Schritten:
1. Ruft [series_decompose ()](series-decomposefunction.md) mit den entsprechenden Parametern auf, um die Basislinie und die Restwerte-Reihe zu erstellen.
1. Berechnet ad_score Reihen, indem [series_outliers ()](series-outliersfunction.md) mit der gewählten anomalieerkennungsmethode für die Restwerte-Reihe angewendet wird.
1. Berechnet die ad_flag Reihe durch Anwenden des Schwellenwerts auf die ad_score, um die Markierung hoch/unten bzw. keine Anomalie zu markieren.
 
## <a name="examples"></a>Beispiele

### <a name="detect-anomalies-in-weekly-seasonality"></a>Erkennen von Anomalien in wöchentlicher Saisonalität

Generieren Sie im folgenden Beispiel eine Reihe mit wöchentlicher Saisonalität, und fügen Sie Ihr dann einige Ausreißer hinzu. `series_decompose_anomalies` erkennt die Saisonalität automatisch und generiert eine Baseline, die das sich wiederholende Muster erfasst. Die Ausreißer, die Sie hinzugefügt haben, können in der ad_score Komponente eindeutig erkannt werden.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers.png" alt-text="Wöchentliche Saison Abhängigkeit, die Baseline und Ausreißer anzeigt" border="false":::

### <a name="detect-anomalies-in-weekly-seasonality-with-trend"></a>Erkennen von Anomalien in wöchentlicher Saisonalität mit Trend

Fügen Sie in diesem Beispiel der Reihe einen Trend aus dem vorherigen Beispiel hinzu. Führen Sie zuerst `series_decompose_anomalies` mit den Standardparametern aus, bei denen der Trend `avg` Standardwert nur den Durchschnitt annimmt und den Trend nicht berechnet. Die generierte Baseline enthält nicht den Trend und ist im Vergleich zum vorherigen Beispiel weniger genau. Folglich werden einige der Ausreißer, die Sie in die Daten eingefügt haben, aufgrund der höheren Varianz nicht erkannt.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers-with-trend.png" alt-text="Wöchentliche Saison Abhängigkeit, die Baseline und Ausreißer anzeigt" border="false":::

Führen Sie als nächstes das gleiche Beispiel aus, aber da Sie einen Trend in der Reihe erwarten, geben Sie `linefit` im Trend Parameter an. Sie können sehen, dass sich die Baseline wesentlich näher an der Eingabe Reihe befindet. Alle eingefügten Ausreißer werden erkannt, und es werden auch einige falsch positive Ergebnisse erkannt. Weitere Informationen zum Optimieren des Schwellenwerts finden Sie im nächsten Beispiel.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 1.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-linefit-trend.png" alt-text="Wöchentliche Saison Abhängigkeit, die Baseline und Ausreißer anzeigt" border="false":::

### <a name="tweak-the-anomaly-detection-threshold"></a>Optimieren des Schwellenwerts für die Anomalieerkennung

Im vorherigen Beispiel wurden einige Laute Punkte als Anomalien erkannt. Erhöhen Sie nun den Schwellenwert für die Anomalieerkennung von dem Standardwert 1,5 auf 2,5. Verwenden Sie diesen interperentil-Bereich, sodass nur stärkere Anomalien erkannt werden. Jetzt werden nur die Ausreißer erkannt, die Sie in die Daten eingefügt haben.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and onlgoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 2.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-higher-threshold.png" alt-text="Wöchentliche Saison Abhängigkeit, die Baseline und Ausreißer anzeigt" border="false":::
