---
title: series_fit_2lines ()-Azure Daten-Explorer
description: In diesem Artikel wird series_fit_2lines () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 00802cc3c90335688a9d56d64eb572e88ecfa15e
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550638"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

Wendet die lineare Regression zweier Segmente auf eine Reihe an und gibt mehrere Spalten zurück.  

Nimmt einen Ausdruck, der das dynamische numerische Array enthält, als Eingabe an und wendet die [lineare Regression zweier Segmente](https://en.wikipedia.org/wiki/Segmented_regression) an, um eine Trend Änderung in einer Reihe zu identifizieren und zu quantifizieren. Die-Funktion durchläuft die Reihen Indizes. In jeder Iterationen teilt die-Funktion die Reihe in zwei Teile auf, passt eine separate Zeile (mithilfe von [series_fit_line ()](series-fit-linefunction.md)) an jeden Teil an und berechnet das gesamte r-Quadrat. Die beste Teilung ist diejenige, die „r-square“ maximiert hat. Die Funktion gibt die dazugehörigen Parameter zurück:


|Parameter  |BESCHREIBUNG  |
|---------|---------|
|`rsquare`     | [R-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) ist das Standardmeasure der fit-Qualität. Dabei handelt es sich um eine Zahl im Bereich [0-1], wobei 1 die bestmögliche Anpassung ist, und 0 bedeutet, dass die Daten unsortiert sind und nicht in eine beliebige Zeile passen.        |
|`split_idx`     |   Der Index des Breakpoints zu zwei Segmenten (null basiert).      |
|`variance`     | Varianz der Eingabedaten.        |
|`rvariance`     | Restvarianz, bei der es sich um die Abweichung zwischen den Eingabedaten Werten (von den beiden Liniensegmenten) handelt.        |
|`line_fit`     | Numerisches Array, das eine Reihe von Werten der am besten angepassten Linie enthält. Die Reihenlänge entspricht der Länge des Eingabearrays. Es wird hauptsächlich zum Charting verwendet.        |
|`right_rsquare`     | R-Quadrat der Zeile auf der rechten Seite der Teilung, siehe [series_fit_line ()](series-fit-linefunction.md).        |
|`right_slope`     | Steigung der rechten angleichen Zeile (in der Form y = ax + b).         |
|`right_interception`     |  Abfang der Näherung der linken linken Linie (b von y = ax + b).       |
|`right_variance`    | Varianz der Eingabedaten auf der rechten Seite der Teilung.        |
|`right_rvariance`     | Verbleibende Varianz der Eingabedaten auf der rechten Seite der Teilung.        |
|`left_rsquare`     | R-Quadrat der Zeile auf der linken Seite der Teilung, siehe [series_fit_line ()](series-fit-linefunction.md).        |
|`left_slope`    | Die Steigung der linken Näherungs Linie (in der Form y = ax + b).        |
|`left_interception`     |   Abfangen der näherenden linken Zeile (in der Form y = ax + b).      |
|`left_variance`     | Varianz der Eingabedaten auf der linken Seite der Teilung.        |
|`left_rvariance`     | Verbleibende Varianz der Eingabedaten auf der linken Seite der Teilung.        |


> [!Note]
> Diese Funktion gibt mehrere Spalten zurück und kann daher nicht als Argument für eine andere Funktion verwendet werden.

**Syntax**

Projekt `series_fit_2lines(` *x*`)`
* Gibt alle oben erwähnten Spalten mit den folgenden Namen zurück: series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx usw.
Project (RS, Si, v) = `series_fit_2lines(` *x*`)`
* Gibt die folgenden Spalten zurück: RS (r-Square), Si (Split Index), v (Varianz) und Rest sehen wie series_fit_2lines_x_rvariance, series_fit_2lines_x_line_fit und usw. erweitern (RS, Si, v) = `series_fit_2lines(` *x* .`)`
* Gibt nur Folgendes zurück: rs (r-square), si (split index) und v (variance).
  
**Argumente**

* *x*: dynamisches Array numerischer Werte.  

> [!TIP]
> Die einfachste Methode, diese Funktion zu verwenden, ist die Anwendung auf die Ergebnisse des Operators " [make-Series](make-seriesoperator.md) ".

**Beispiele**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="Reihen passend 2 Zeilen":::
