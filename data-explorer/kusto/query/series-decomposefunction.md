---
title: series_decompose() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_decompose() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 375d63dd050840cd884fca0198511e71ac46f170
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663609"
---
# <a name="series_decompose"></a>series_decompose()

Wendet eine Zersetzungstransformation auf eine Serie an.  

Nimmt einen Ausdruck, der eine Reihe (dynamisches numerisches Array) enthält, als Eingabe und zerlegt ihn in saisonale, Trend- und Restkomponenten.
 
**Syntax**

`series_decompose(`*Serie* `[,` *Saisonalität* `,` *Trend* `,` *Test_points* `,` *Seasonality_threshold*`])`

**Argumente**

* *Serie*: Dynamische Arrayzelle, die ein Array numerischer Werte ist, in der Regel die resultierende Ausgabe von [Make-Series](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md) Operatoren
* *Saisonalität*: Eine ganze Zahl, die die saisonale Analyse kontrolliert und entweder
    * -1: Saisonalität mit [series_periods_detect](series-periods-detectfunction.md) automatisch erkennen (Standard).
    * Periode: positive ganze Zahl, die die erwartete Periode in der Anzahl der Lagerplätze angibt. Wenn sich die Serie z. B. in 1h-Abschnitten befindet, beträgt eine wöchentliche Periode 168 Abschnitte.
    * 0: keine Saisonalität (überspringen Sie das Extrahieren dieser Komponente).    
* *Trend*: Eine Zeichenfolge, die die Trendanalyse steuert und eine der folgenden Optionen enthält:
    * "avg": Definieren der Trendkomponente als Average(x) (Standard)
    * "linefit": Extrahieren Sie die Trendkomponente mithilfe der linearen Regression.
    * "none": kein Trend, überspringen Sie das Extrahieren dieser Komponente.    
* *Test_points*: 0 (Standard) oder positive ganze Zahl, die Die Anzahl der Punkte am Ende der Reihe angibt, die vom Lernprozess (Regression) ausgeschlossen werden sollen. Dieser Parameter sollte für Prognosezwecke festgelegt werden.
* *Seasonality_threshold*: Der Schwellenwert für die Saisonalitätsbewertung, wenn *Saisonalität* `0.6`auf Auto-Erkennung festgelegt ist, ist der Standardwertschwellenwert . Weitere Informationen finden [Sie unter series_periods_detect](series-periods-detectfunction.md).

**Rückgabe**

 Die Funktion gibt folgende Serien zurück:

* `baseline`: der vorhergesagte Wert der Reihen (Summe der saisonalen und Trendkomponenten, siehe unten)
* `seasonal`: die Reihen der saisonalen Komponente:
    * Wenn der Zeitraum nicht erkannt oder explizit auf 0 gesetzt wird: Konstante 0
    * Wenn erkannt oder auf positive GanzerZahl gesetzt: Median der Serienpunkte in derselben Phase
* `trend`: die Reihe der Trendkomponente
* `residual`: die Reihe der Restkomponente (d. h. x - Basislinie)
  

**Hinweise**

* Komponentenausführungsreihenfolge:
    1. Extrahieren Sie die saisonale Serie
    2. Subtrahieren Sie es von x, wodurch die Saisonserie erzeugt wird
    3. Extrahieren Sie die Trendkomponente aus der Saisonserie
    4. Erstellen der Basislinie = saisonal + Trend
    5. Erstellen des Rests = x - Baseline
    
* Entweder Saisonalität und/oder Trend sollten aktiviert sein, andernfalls ist die Funktion redundant und gibt nur Baseline = 0 und Residual = x zurück.

**Mehr über Serienzerlegung**

Diese Methode wird in der Regel auf Zeitreihen von Metriken angewendet, von der erwartet wird, dass sie periodisches und/oder Trendverhalten manifestiert (z. B. Dienstdatenverkehr und andere Nutzungsmetriken), um entweder zukünftige Metrikwerte vorherzusagen und/oder anomale Zuerkennen. Die implizite Annahme dieses Regressionsprozesses ist, dass abgesehen vom (a-priori bekannten) saisonalen und Trendverhalten die Zeitreihen stochastisch und zufällig verteilt sind; Folglich können wir zukünftige Metrikwerte aus den saisonalen und Trendkomponenten prognostizieren (das Restteil ignorieren), während wir anomale Werte auf der Grundlage der Ausreißererkennung nur auf dem Restteil erkennen können. Weitere Details finden Sie im [Kapitel Zeitreihenzerlegung](https://www.otexts.org/fpp/6) dieses großartigen Buches.

**Beispiele**

**1. Wöchentliche Saisonalität**

Im folgenden Beispiel generieren wir eine Serie mit wöchentlicher Saisonalität und ohne Trend fügen wir dann einige Ausreißer hinzu. `series_decompose`findet die Auto-Erkennung der Saisonalität und generiert eine Basislinie, die fast identisch mit der saisonalen Komponente ist. Die Vonreißer, die wir hinzugefügt haben, sind in der Residuenkomponente deutlich zu sehen.

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

:::image type="content" source="images/samples/series-decompose1.png" alt-text="Serie zersetzen 1":::

**2. Wöchentliche Saisonalität mit Trend**

In diesem Beispiel fügen wir der Reihe einen Trend aus dem vorherigen Beispiel hinzu. Zuerst werden `series_decompose` wir mit den Standardparametern ausgeführt, bei denen der Trend-Standardwert `avg` nur den Durchschnitt nimmt und den Trend nicht berechnet, wir können sehen, dass die generierte Baseline den Trend nicht enthält und im Vergleich zum vorherigen Beispiel weniger genau ist, wird am deutlichsten, wenn der Trend in den Residuen beobachtet wird.

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

:::image type="content" source="images/samples/series-decompose2.png" alt-text="Serie zersetzen 2":::

Als Nächstes führen wir das gleiche Beispiel aus, aber da `linefit` wir einen Trend in der Serie erwarten, geben wir im Trendparameter an. Wir können sehen, dass der positive Trend erkannt wird und die Basislinie viel näher an der Eingabereihe liegt. Die Residuen liegen nahe Null, wobei nur die Ausreißer herausfallen. Wir können alle Komponenten auf der Serie im Diagramm sehen.

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

:::image type="content" source="images/samples/series-decompose3.png" alt-text="Serie zersetzen 3":::
