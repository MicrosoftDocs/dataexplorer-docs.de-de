---
title: series_decompose_anomalies ()-Azure Daten-Explorer
description: In diesem Artikel wird series_decompose_anomalies () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 0cd2fc2e395ad47cff29589298ba7ae694fce941
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372904"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

Anomalieerkennung basierend auf der Reihen Zerlegung (siehe [series_decompose ()](series-decomposefunction.md)) 

Nimmt einen Ausdruck, der eine Reihe (dynamisches numerisches Array) enthält, als Eingabe auf und extrahiert anormale Punkte mit Bewertungen.

**Syntax**

`series_decompose_anomalies (`*Reihe* `[, ` *Schwellenwert* `,` *Saisonalität* `,` *Trend* `, ` *Test_points* `, ` *AD_method* `,` *Seasonality_threshold*`])`

**Argumente**

* *Series*: dynamische Array Zelle, bei der es sich um ein Array numerischer Werte handelt, normalerweise die resultierende Ausgabe von [make-Series-](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md) -Operatoren
* *Schwellen*Wert: anomalieschwellen Wert, Standardwert 1,5 (k Wert) zum Erkennen von milden oder stärkeren Anomalien
* *Saison*Abhängigkeit: eine ganze Zahl, die die saisonale Analyse steuert, die Folgendes enthält:
    * -1: automatische Erkennung der Saisonalität (mit [series_periods_detect](series-periods-detectfunction.md)) [Standard] 
    * 0: keine Saisonalität (d. h. das Extrahieren dieser Komponente überspringen)
    * Period: eine positive ganze Zahl, die den erwarteten Zeitraum in der Anzahl der Containereinheiten angibt. Wenn sich die Reihe z. b. in 1-Stunden-Containern befindet, ist ein wöchentlicher Zeitraum 168 Behälter.
* *Trend*: eine Zeichenfolge, die die Trend Analyse steuert, die Folgendes enthält:    
    * "AVG": Trend Komponente als Mittelwert der Reihe definieren [Standard]
    * "None": kein Trend, Extrahieren dieser Komponente überspringen 
    * "Linefit": Trend Komponente mithilfe von linearer Regression extrahieren
* *Test_points*: 0 [Standard] oder eine positive ganze Zahl, die die Anzahl der Punkte am Ende der Reihe angibt, die vom Lernprozess (Regression) ausgeschlossen werden sollen. Dieser Parameter sollte für Vorhersagezwecke festgelegt werden.
* *AD_method*: eine Zeichenfolge, die die anomalieerkennungsmethode (siehe [series_outliers](series-outliersfunction.md)) für die restliche Zeitreihe steuert, die Folgendes enthält:    
    * "ctukey": [Tukey-Fence-Test](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) mit benutzerdefiniertem 10.90. Perzentil-Bereich [Standard]
    * "Tukey": [der "Tukey](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) "-Fence-Test mit dem standardmäßigen 25.75. Perzentil-Bereich
* *Seasonality_threshold*: der Schwellenwert für die saisonalitätsbewertung, wenn *Saisonalität* auf Autodetect festgelegt ist, ist der Standardwert für die Bewertung `0.6` (Weitere Informationen finden Sie unter: [series_periods_detect](series-periods-detectfunction.md))


**Hre**

 Die-Funktion gibt die folgende Reihe zurück:

* `ad_flag`: eine ternäre Reihe mit (+ 1,-1, 0), die eine Markierung nach oben/unten/keine Anomalie enthält
* `ad_score`: anomaliebewertung
* `baseline`: der vorhergesagte Wert der Reihe gemäß der Zerlegung

**Weitere Informationen zum Algorithmus**

Diese Funktion folgt den folgenden Schritten:
1. Ruft [series_decompose ()](series-decomposefunction.md) mit den entsprechenden Parametern auf, um die Baseline und die Restwerte-Reihe zu erstellen.
2. Berechnet ad_score Reihen, indem [series_outliers ()](series-outliersfunction.md) mit der gewählten anomalieerkennungsmethode für die Restwerte-Reihe angewendet wird.
3. Berechnet die ad_flag Reihe durch Anwenden des Schwellenwerts auf die ad_score, um die Markierung hoch/unten/keine Anomalie zu markieren.
 
**Beispiele**

**1. erkennen von Anomalien in wöchentlicher Saisonalität**

Im folgenden Beispiel wird eine Reihe mit wöchentlicher Saison Abhängigkeit generiert. Anschließend fügen wir einige Ausreißer hinzu. `series_decompose_anomalies`erkennt die Saisonalität automatisch und generiert eine Baseline, die das sich wiederholende Muster erfasst. Die Ausreißer, die wir hinzugefügt haben, können in der ad_score Komponente eindeutig erkannt werden.

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

**2. erkennen von Anomalien in wöchentlicher Saisonalität mit Trend**

In diesem Beispiel fügen wir der Reihe einen Trend aus dem vorherigen Beispiel hinzu. Zuerst führen wir `series_decompose_anomalies` mit den Standardparametern aus, bei denen der `avg` Standardwert des Trends nur den Mittelwert einnimmt und den Trend nicht berechnet. Wir können sehen, dass die generierte Baseline nicht den Trend enthält und im Vergleich zum vorherigen Beispiel weniger genau ist. Folglich werden einige der Ausreißer, die wir in die Daten eingefügt haben, aufgrund der höheren Varianz nicht erkannt.

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

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers-with-trend.png" alt-text="Wöchentliche saisonalitätsausreißer mit Trend" border="false":::

Als nächstes führen wir das gleiche Beispiel aus, aber da wir einen Trend in der Reihe erwarten, legen wir `linefit` im Trend Parameter fest. Wir können sehen, dass sich die Baseline wesentlich näher an der Eingabe Reihe befindet. Alle Ausreißer, die Sie eingefügt haben, werden erkannt, ebenso wie einige falsch positive Ergebnisse (siehe das nächste Beispiel zum Optimieren des Schwellenwerts).

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

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-linefit-trend.png" alt-text="Wöchentliche saisonitäts Anomalien mit Linefit-Trend" border="false":::

**3. Optimieren des Schwellenwerts für die Anomalieerkennung**

Im vorherigen Beispiel wurden einige Laute Punkte als Anomalien erkannt. in diesem Beispiel wird der Schwellenwert für die Anomalieerkennung von einem Standardwert von 1,5 auf 2,5 des interperentil-Bereichs erhöht, sodass nur stärkere Anomalien erkannt werden. Wir können sehen, dass jetzt nur die Ausreißer erkannt werden, die in die Daten eingefügt wurden.

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

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-higher-threshold.png" alt-text="Abweichungen bei wöchentlichen Reihen mit höherem anomalieschwellen Wert" border="false":::

