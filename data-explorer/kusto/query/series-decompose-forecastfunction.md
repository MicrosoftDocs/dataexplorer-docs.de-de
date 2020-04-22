---
title: series_decompose_forecast() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_decompose_forecast() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 67f464949bbc432e73932c4d8bff8290ef8f0f8f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663544"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

Prognose basierend auf Serienzerlegung.

Nimmt einen Ausdruck, der eine Reihe (dynamisches numerisches Array) enthält, als Eingabe und prognostiziere die Werte der letzten nachgestellten Punkte (weitere Details zur Zersetzungsmethode finden Sie in [series_decompose).](series-decomposefunction.md)
 
**Syntax**

`series_decompose_forecast(`*Serienpunkte* `,` `[,` *Saisonalität* `,` *Points* *Trend* `,` *Seasonality_threshold*`])`

**Argumente**

* *Serie*: Dynamische Arrayzelle, die ein Array numerischer Werte ist. In der Regel die resultierende Ausgabe von [Make-Series](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md) Operatoren.
* *Punkte*: Ganzzahl, die die Anzahl der Punkte am Ende der Reihe angibt, die vorhergesagt werden sollen (Prognose). Diese Punkte sind vom Lernprozess (Regression) ausgeschlossen.
* *Saisonalität*: Eine ganze Zahl, die die saisonale Analyse kontrolliert und eine der folgenden Optionen enthält:
    * -1: Saisonalität mit [series_periods_detect](series-periods-detectfunction.md) automatisch erkennen (Standard). 
    * Periode: positive ganze Zahl, die die erwartete Periode in der Anzahl der Lagerplätze angibt. Wenn sich die Serie z. B. in 1h-Abschnitten befindet, beträgt eine wöchentliche Periode 168 Abschnitte.
    * 0: keine Saisonalität (überspringen Sie das Extrahieren dieser Komponente).   
* *Trend*: Eine Zeichenfolge, die die Trendanalyse steuert und eine der folgenden Optionen enthält:
    * "linefit": Extrahieren Sie die Trendkomponente mithilfe der linearen Regression (Standard).    
    * "avg": Definieren Sie trendkomponente als average(x).
    * "none": kein Trend, überspringen Sie das Extrahieren dieser Komponente.   
* *Seasonality_threshold*: Der Schwellenwert für die Saisonalitätsbewertung, wenn *Saisonalität* `0.6`auf Auto-Erkennung festgelegt ist, ist der Standardwertschwellenwert . Weitere Informationen finden Sie [unter series_periods_detect](series-periods-detectfunction.md).

**Rückgabe**

 Ein dynamisches Array mit der prognostizierten Serie
  

**Hinweise**

* Das dynamische Array der ursprünglichen Eingabereihe sollte einen zu prognostizierenden *Anzahlpunkt-Slots* enthalten, dies geschieht in der Regel, indem die [Serien](make-seriesoperator.md) verwendet und die Endzeit im Bereich angegeben wird, die den Zeitrahmen für die Prognose enthält.
    
* Entweder Saisonalität und/oder Trend sollten aktiviert werden, andernfalls ist die Funktion redundant und gibt nur eine Reihe mit Nullen zurück.

**Beispiel**

Im folgenden Beispiel erzeugen wir eine Serie von 4 Wochen in einem stündlichen Korn `make-series` mit wöchentlicher Saisonalität und einem kleinen Aufwärtstrend, dann verwenden und fügen wir eine weitere leere Woche in die Serie. `series_decompose_forecast`wird mit einer Woche (24 * 7 Punkte) aufgerufen, erkennt automatisch die Saisonalität und den Trend und generiert eine Prognose für den gesamten Zeitraum von 5 Wochen. 

```kusto
let ts=range t from 1 to 24*7*4 step 1 // generate 4 weeks of hourly data
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| make-series y=max(y) on Timestamp in range(datetime(2018-03-01 05:00), datetime(2018-03-01 05:00)+24*7*5h, 1h); // create a time series of 5 weeks (last week is empty)
ts 
| extend y_forcasted = series_decompose_forecast(y, 24*7)  // forecast a week forward
| render timechart 
```

:::image type="content" source="images/samples/series-decompose-forecast.png" alt-text="Serienzerlegen egnostieren Prognose":::
