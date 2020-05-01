---
title: series_decompose_forecast ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird series_decompose_forecast () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 97f87a7390ab099886e84642b2eb46a8087b6da9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618841"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

Vorhersage basierend auf der Reihen Zerlegung.

Nimmt einen Ausdruck, der eine Reihe (dynamisches numerisches Array) enthält, als Eingabe auf und prognostiziert die Werte der letzten nachfolgenden Punkte (Weitere Informationen zur Zerlegungs Methode finden Sie in der [series_decompose](series-decomposefunction.md) ).
 
**Syntax**

`series_decompose_forecast(`*Series* `,` *Points* `[,` `,` *Trend* Trend`,` der *Saisonalität* für Reihen Punkte *Seasonality_threshold*`])`

**Argumente**

* *Series*: dynamische Array Zelle, bei der es sich um ein Array numerischer Werte handelt. In der Regel die resultierende Ausgabe von [make-Series-](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md) -Operatoren.
* *Points*: ganze Zahl, die die Anzahl der Punkte am Ende der vorher zusagenden Reihe angibt (Vorhersage). Diese Punkte werden vom Lernprozess (Regression) ausgeschlossen.
* *Saison*Abhängigkeit: eine ganze Zahl, die die saisonale Analyse steuert und eine der folgenden Komponenten enthält:
    * -1: automatische Erkennung der Saisonalität mithilfe von [series_periods_detect](series-periods-detectfunction.md) (Standard). 
    * Period: eine positive ganze Zahl, die den erwarteten Zeitraum in der Anzahl von Containern angibt. Wenn sich die Reihe z. b. in 1-Stunden-Containern befindet, beträgt die wöchentliche Zeit 168 Behälter.
    * 0: keine Saisonalität (Extrahieren dieser Komponente überspringen).   
* *Trend*: eine Zeichenfolge, die die Trend Analyse steuert und eine der folgenden Zeichen folgen enthält:
    * "Linefit": Trend Komponente mithilfe von linearer Regression extrahieren (Standard).    
    * "AVG": Hiermit wird die Trend Komponente als Durchschnitt (x) definiert.
    * "None": kein Trend, Extrahieren dieser Komponente überspringen.   
* *Seasonality_threshold*: der Schwellenwert für die saisonalitätsbewertung, wenn *Saison alität* auf automatische Erkennung festgelegt ist, ist `0.6`der Standardwert für die Bewertung. Weitere Informationen finden Sie unter [series_periods_detect](series-periods-detectfunction.md).

**Hre**

 Ein dynamisches Array mit der vorhergesagten Reihe
  

**Hinweise**

* Das dynamische Array der ursprünglichen Eingabe Reihe sollte *eine Reihe von* Slots enthalten, die vorhergesagt werden müssen. Dies erfolgt in der Regel mithilfe von [make-Series](make-seriesoperator.md) und durch Angeben der Endzeit im Bereich, die den Zeitrahmen für die Vorhersage umfasst.
    
* Die Saison Abhängigkeit und/oder der Trend sollten aktiviert werden, andernfalls ist die Funktion redundant und gibt nur eine Reihe zurück, die mit Nullen gefüllt ist.

**Beispiel**

Im folgenden Beispiel generieren wir eine Reihe von 4 Wochen in stündlicher Abhängigkeit mit wöchentlicher Saison Abhängigkeit und einem kleinen Aufwärtstrend. Anschließend verwenden `make-series` wir und fügen der Reihe eine weitere leere Woche hinzu. `series_decompose_forecast`wird mit einer Woche (24 * 7 Punkte) aufgerufen und erkennt automatisch die Saisonalität und den Trend und generiert eine Vorhersage des gesamten Zeitraums von 5 Wochen. 

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

:::image type="content" source="images/series-decompose-forecastfunction/series-decompose-forecast.png" alt-text="Vorhersage der Reihen Erstellung":::
