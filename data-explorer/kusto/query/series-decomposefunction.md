---
title: series_decompose ()-Azure Daten-Explorer
description: In diesem Artikel wird series_decompose () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 9ff0df578f174bc6964e39e799b91068f89a28e4
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793948"
---
# <a name="series_decompose"></a>series_decompose()

Wendet eine Zerlegungs Transformation auf eine Reihe an.  

Nimmt einen Ausdruck, der eine Reihe (dynamisches numerisches Array) enthält, als Eingabe auf und zerlegt ihn in saisonale, Trend-und Rest-Komponenten.
 
## <a name="syntax"></a>Syntax

`series_decompose(`*Reihe* `[,` *Saisonalität* `,` *Trend* `,` *Test_points* `,` *Seasonality_threshold*`])`

## <a name="arguments"></a>Argumente

* *Series*: dynamische Array Zelle, bei der es sich um ein Array numerischer Werte handelt, in der Regel die resultierende Ausgabe von [make-Series-](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md) -Operatoren
* *Saison*Abhängigkeit: eine ganze Zahl, die die saisonale Analyse steuert, die Folgendes enthält:
    * -1: automatische Erkennung der Saisonalität mithilfe von [series_periods_detect](series-periods-detectfunction.md) (Standard).
    * Period: eine positive ganze Zahl, die den erwarteten Zeitraum in der Anzahl der Container angibt. Wenn sich die Reihe z. b. in 1-Stunden-Containern befindet, beträgt die wöchentliche Zeit 168.
    * 0: keine Saisonalität (Extrahieren dieser Komponente überspringen).    
* *Trend*: eine Zeichenfolge, die die Trend Analyse steuert und einen der folgenden Werte enthält:
    * "AVG": Trend Komponente als Average (x) definieren (Standard)
    * "Linefit": Trend Komponente mithilfe von linearer Regression extrahieren.
    * "None": kein Trend, Extrahieren dieser Komponente überspringen.    
* *Test_points*: 0 (Standard) oder positive ganze Zahl, die die Anzahl der Punkte am Ende der Reihe angibt, die vom Lernprozess (Regression) ausgeschlossen werden sollen. Dieser Parameter sollte für Vorhersagezwecke festgelegt werden.
* *Seasonality_threshold*: der Schwellenwert für die saisonalitätsbewertung, wenn *Saison alität* auf Autodetect festgelegt ist, ist der Standardwert für die Bewertung `0.6` . Weitere Informationen finden Sie unter [series_periods_detect](series-periods-detectfunction.md).

**Return**

 Die-Funktion gibt die folgende Reihe zurück:

* `baseline`: der vorhergesagte Wert der Reihe (Summe der Saison-und Trend Komponenten, siehe unten).
* `seasonal`: die Reihe der saisonalen Komponente:
    * , wenn der Zeitraum nicht erkannt wird oder explizit auf 0 festgelegt ist: Konstante 0.
    * Wenn erkannt oder auf positive ganze Zahl festgelegt: Median der Reihen Punkte in derselben Phase
* `trend`: die Reihe der Trend Komponente.
* `residual`: die Reihe der Rest-Komponente (d. h. x-Baseline).
  

**Notizen**

* Ausführungsreihenfolge der Komponente:
    1. Extrahieren der Saison Serie
    2. Subtrahieren Sie es von x, um die desaisonale Reihe zu erstellen
    3. Extrahieren der Trend Komponente aus der Saison Reihe
    4. Erstellen der Baseline = Saison + Trend
    5. Create the Restwert = x-Baseline
    
* Entweder Saison Abhängigkeit und oder Trend sollten aktiviert sein. Andernfalls ist die Funktion redundant und gibt lediglich Baseline = 0 und Restwert = x zurück.

**Weitere Informationen zur Reihen Zerlegung**

Diese Methode wird in der Regel auf Zeitreihen von Metriken angewendet, die das periodische und/oder Trend Verhalten manifestieren. Sie können die-Methode verwenden, um zukünftige Metrikwerte zu prognostizieren und/oder anomale Werte zu erkennen. Die implizite Annahme dieses Regressions Prozesses ist, dass die Zeitreihe außer dem saisonalen und dem Trend Verhalten stochastisch und nach dem Zufallsprinzip verteilt ist. Prognostizieren Sie zukünftige Metrikwerte aus den Saison-und Trend Komponenten, während Sie den Restteil ignorieren. Erkennen anormaler Werte auf der Grundlage der Ausreißererkennung nur für den Restteil. Weitere Informationen finden Sie im [Kapitel Zeitreihen Zerlegung](https://otexts.com/fpp2/decomposition.html).

## <a name="examples"></a>Beispiele

**Wöchentliche Saison Abhängigkeit**

Im folgenden Beispiel wird eine Reihe mit wöchentlicher Saison Abhängigkeit und ohne Trend generiert. Anschließend werden einige Ausreißer hinzugefügt. `series_decompose` findet und erkennt automatisch die Saisonalität und generiert eine Baseline, die fast identisch mit der Saison Komponente ist. Die Ausreißer, die wir hinzugefügt haben, können eindeutig in der Komponente Restwerte angezeigt werden.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose1.png" alt-text="Reihen Komposition 1":::

**Wöchentliche Saisonalität mit Trend**

In diesem Beispiel fügen wir der Reihe einen Trend aus dem vorherigen Beispiel hinzu. Zuerst führen wir `series_decompose` mit den Standardparametern aus. Der Standardwert für den Trend `avg` übernimmt nur den Mittelwert und berechnet nicht den Trend. Die generierte Baseline enthält keinen Trend. Wenn Sie den Trend in den residuals beobachten, wird deutlich, dass dieses Beispiel weniger genau ist als im vorherigen Beispiel.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose2.png" alt-text="Reihen Komposition 2":::

Als nächstes führen wir das gleiche Beispiel erneut aus. Da wir einen Trend in der Reihe erwarten, legen wir `linefit` im Trend Parameter fest. Wir können sehen, dass der positive Trend erkannt wird und sich die Baseline wesentlich näher an der Eingabe Reihe befindet. Die Restwerte liegen nahe bei Null, und nur die Ausreißer sind hervorragend. Wir sehen alle Komponenten in der Reihe im Diagramm.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y, -1, 'linefit')
| render timechart  
```

:::image type="content" source="images/samples/series-decompose3.png" alt-text="Reihen Komposition 3":::
