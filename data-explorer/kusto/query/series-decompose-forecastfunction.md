---
title: series_decompose_forecast ()-Azure Daten-Explorer
description: In diesem Artikel wird series_decompose_forecast () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 21a57e4c49e982fbb113917abe173f89426c74ed
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345146"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

Vorhersage basierend auf der Reihen Zerlegung.

Nimmt einen Ausdruck, der eine Reihe (dynamisches numerisches Array) enthält, als Eingabe auf und prognostiziert die Werte der letzten nachfolgenden Punkte. Weitere Informationen finden Sie unter [series_decompose](series-decomposefunction.md).
 
## <a name="syntax"></a>Syntax

`series_decompose_forecast(`*Reihe* `,` *Punkte* `[,` *Saisonalität* `,` *Trend* `,` *Seasonality_threshold*`])`

## <a name="arguments"></a>Argumente

* *Series*: dynamische Array Zelle numerischer Werte. In der Regel die resultierende Ausgabe von [make-Series-](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md) -Operatoren.
* *Points*: ganze Zahl, die die Anzahl der Punkte am Ende der vorher zusagenden Reihe angibt (Vorhersage). Diese Punkte werden vom Lernprozess (Regression) ausgeschlossen.
* *Saison*Abhängigkeit: eine ganze Zahl, die die saisonale Analyse steuert, die Folgendes enthält:
    * -1: automatische Erkennung der Saisonalität mithilfe von [series_periods_detect](series-periods-detectfunction.md) (Standard).
    * Period: eine positive ganze Zahl, die den erwarteten Zeitraum in der Anzahl von Containern angibt. Wenn sich die Reihe z. b. in 1-Stunden-Containern befindet, beträgt die wöchentliche Zeit 168 Behälter.
    * 0: keine Saisonalität (Extrahieren dieser Komponente überspringen).
* *Trend*: eine Zeichenfolge, die die Trend Analyse steuert, die Folgendes enthält:
    * `linefit`: Extrahieren der Trend Komponente mithilfe der linearen Regression (Standard).
    * `avg`: Definieren Sie die Trend Komponente als Mittelwert (x).
    * `none`: Kein Trend, Extrahieren dieser Komponente überspringen.
* *Seasonality_threshold*: der Schwellenwert für die saisonalitätsbewertung, wenn *Saison alität* auf Autodetect festgelegt ist. Der Standardwert für das Ergebnis ist `0.6` . Weitere Informationen finden Sie unter [series_periods_detect](series-periods-detectfunction.md).

**Rückgabewert**

 Ein dynamisches Array mit der vorhergesagten Reihe.

> [!NOTE]
> * Das dynamische Array der ursprünglichen Eingabe Reihe sollte eine Reihe von *Punkt* Slots enthalten, die vorhergesagt werden sollen. Die Vorhersage erfolgt in der Regel mithilfe von [make-Series](make-seriesoperator.md) und durch Angabe der Endzeit im Bereich, die den Zeitrahmen für die Vorhersage umfasst.
> * Die Saison Abhängigkeit oder der Trend sollte aktiviert werden, andernfalls ist die Funktion redundant und gibt nur eine Reihe zurück, die mit Nullen gefüllt ist.

## <a name="example"></a>Beispiel

Im folgenden Beispiel generieren wir eine Reihe von vier Wochen in stündlicher Abhängigkeit mit wöchentlicher Saisonalität und einem kleinen Aufwärtstrend. Wir verwenden dann `make-series` und fügen der Reihe eine weitere leere Woche hinzu. `series_decompose_forecast`wird mit einer Woche (24 * 7 Punkte) aufgerufen und erkennt automatisch die Saisonalität und den Trend und generiert eine Vorhersage für den gesamten fünfwöchigen Zeitraum.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
 
