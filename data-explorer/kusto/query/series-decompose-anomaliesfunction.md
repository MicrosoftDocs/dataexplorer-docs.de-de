---
title: series_decompose_anomalies() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden series_decompose_anomalies() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 7d9ca0585b40a97d21690f908a50de5be493c412
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663624"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

Anomaly Detection basierend auf Serienzersetzung (siehe [series_decompose()](series-decomposefunction.md)) 

Nimmt einen Ausdruck, der eine Reihe (dynamisches numerisches Array) enthält, als Eingabe und extrahiert anomale Punkte mit Partituren.

**Syntax**

`series_decompose_anomalies (`*Serie* `[, ` *Schwelle* `,` *Saisonalität* `,` *Trend* `, ` *Test_points* `, ` *AD_method* `,` *Seasonality_threshold*`])`

**Argumente**

* *Serie*: Dynamische Arrayzelle, die ein Array numerischer Werte ist, in der Regel die resultierende Ausgabe von [Make-Series](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md) Operatoren
* *Schwellenwert*: Anomalieschwelle, Standard wert 1,5 (k-Wert) zur Erkennung leichter oder stärkerer Anomalien
* *Saisonalität*: Eine ganze Zahl, die die saisonale Analyse kontrolliert und entweder
    * -1: Saisonalität automatisch erkennen (mit [series_periods_detect](series-periods-detectfunction.md)) [Standard] 
    * 0: keine Saisonalität (d. h. Überspringen des Extrahierens dieser Komponente)
    * Periode: positive ganze Zahl, die angabe der erwarteten Periode in der Anzahl der Lagerplatzeinheit. Wenn sich die Serie z. B. in 1h-Abschnitten befindet, beträgt eine wöchentliche Periode 168 Abschnitte.
* *Trend*: Eine Zeichenfolge, die die Trendanalyse steuert und entweder    
    * "avg": Definieren der Trendkomponente als Durchschnitt der Reihe [Standard]
    * "none": kein Trend, überspringen Sie das Extrahieren dieser Komponente 
    * "linefit": Trendkomponente mithilfe linearer Regression extrahieren
* *Test_points*: 0 [Standard] oder positive ganze Zahl, die Die Anzahl der Punkte am Ende der Reihe angibt, die vom Lernprozess (Regression) ausgeschlossen werden sollen. Dieser Parameter sollte für Prognosezwecke festgelegt werden.
* *AD_method*: Eine Zeichenfolge, die die Anomalieerkennungsmethode (siehe [series_outliers](series-outliersfunction.md)) auf der Restzeitreihe steuert,    
    * "ctukey": [Tukeys Zauntest](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) mit benutzerdefiniertem 10.-90. Perzentilbereich [Standard]
    * "tukey": [Tukeys Zauntest](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) mit Standard25-75. Perzentilbereich
* *Seasonality_threshold*: Der Schwellenwert für die Saisonalitätsbewertung, wenn *Saisonalität* auf Autoerkennung festgelegt ist, ist `0.6` der Standardwertschwellenwert (weitere Details siehe: [series_periods_detect](series-periods-detectfunction.md))


**Rückgabe**

 Die Funktion gibt folgende Serien zurück:

* `ad_flag`: eine ternäre Serie mit (+1, -1, 0), die nach oben/unten/keine Anomalie markiert
* `ad_score`: Anomalie-Punktzahl
* `baseline`: der vorhergesagte Wert der Reihe nach der Zersetzung

**Mehr über den Algorithmus**

Diese Funktion folgt den folgenden Schritten:
1. Ruft [series_decompose()](series-decomposefunction.md) mit den entsprechenden Parametern auf, um die Basislinie und die Residuenreihe zu erstellen
2. Berechnet ad_score Reihe, indem [series_outliers()](series-outliersfunction.md) mit der gewählten Anomalieerkennungsmethode auf die Residuenserie angewendet wird
3. Berechnet die ad_flag Reihe, indem der Schwellenwert auf die ad_score angewendet wird, um eine Anomalie nach oben/unten/keine zu markieren.
 
**Beispiele**

**1. Erkennen von Anomalien in der wöchentlichen Saisonalität**

Im folgenden Beispiel generieren wir eine Serie mit wöchentlicher Saisonalität, dann fügen wir einige Ausreißer hinzu. `series_decompose_anomalies`erkennt die Saisonalität automatisch und generiert eine Baseline, die das sich wiederholende Muster erfasst. Die Ausreißer, die wir hinzugefügt haben, können in der ad_score-Komponente deutlich erkannt werden.

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

:::image type="content" source="images/samples/series-decompose-anomalies1.png" alt-text="Serie zersetzen Anomalien 1":::

**2. Erkennen von Anomalien in der wöchentlichen Saisonalität mit Trend**

In diesem Beispiel fügen wir der Reihe einen Trend aus dem vorherigen Beispiel hinzu. Zuerst werden `series_decompose_anomalies` wir mit den Standardparametern ausgeführt, bei denen der Trend-Standardwert `avg` nur den Durchschnitt nimmt und den Trend nicht berechnet, wir können sehen, dass die generierte Baseline den Trend nicht enthält und im Vergleich zum vorherigen Beispiel weniger genau ist, so dass einige der Ausreißer, die wir in die Daten eingefügt haben, aufgrund der höheren Varianz nicht erkannt werden.

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
:::image type="content" source="images/samples/series-decompose-anomalies2.png" alt-text="Serie zersetzen Anomalien 2":::

Als Nächstes führen wir das gleiche Beispiel aus, aber da `linefit` wir einen Trend in der Serie erwarten, geben wir im Trendparameter an. Wir können sehen, dass die Basislinie viel näher an der Eingabereihe liegt. Alle Ausreißer, die wir eingefügt haben, werden erkannt, sowie einige falsche Positive (siehe nächstes Beispiel zum Optimieren des Schwellenwerts).

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

:::image type="content" source="images/samples/series-decompose-anomalies3.png" alt-text="Serie zersetzen Anomalien 3":::

**3. Optimierung des Schwellenwerts für die Erkennung von Anomalien**

Im vorherigen Beispiel wurden einige laute Punkte als Anomalien erkannt, in diesem Beispiel erhöhen wir den Schwellenwert für die Anomalieerkennung von einem Standardwert von 1,5 auf 2,5 im Interperpertilbereich, sodass nur stärkere Anomalien erkannt werden. Wir können sehen, dass jetzt nur die Ausreißer, die wir in die Daten eingefügt haben, erkannt werden.

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

:::image type="content" source="images/samples/series-decompose-anomalies4.png" alt-text="Serie zersetzen Anomalien 4":::
