---
title: series_fit_2lines() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_fit_2lines() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 05163735c88c3229c13d76e3f8fde9bff091b239
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663505"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

Wendet zwei Segmente lineare Regression auf eine Reihe an und gibt mehrere Spalten zurück.  

Nimmt einen Ausdruck, der dynamisches numerisches Array als Eingabe enthält, und wendet [zwei Segmente lineare Regression](https://en.wikipedia.org/wiki/Segmented_regression) an, um Trendänderungen in einer Reihe zu identifizieren und zu quantifizieren. Die Funktion iteriert auf den Reihenindizes und teilt in jeder Iteration die Reihe in 2 Teile auf, passt eine separate Zeile (mit [series_fit_line()](series-fit-linefunction.md)) zu jedem Teil und berechnet die Gesamtr-Quadrat. Die beste Teilung ist diejenige, die „r-square“ maximiert hat. Die Funktion gibt die dazugehörigen Parameter zurück:
* `rsquare`: [r-square](https://en.wikipedia.org/wiki/Coefficient_of_determination) ist ein Standardmaß für die Passformqualität. Es ist eine Zahl im Bereich [0-1], wobei 1 die beste Option ist und 0 bedeutet, dass die Daten unsortiert sind und nicht in jede beliebige Zeile passen.
* `split_idx`: der Index des Bruchpunkts auf 2 Segmente (nullbasiert)
* `variance`: Varianz der Eingabedaten
* `rvariance`: Restabweichung, d. h. die Varianz zwischen den Eingabedaten, wertet die angenäherten Werte (durch die 2 Liniensegmente).
* `line_fit`: numerisches Array mit einer Reihe von Werten der am besten angepassten Linie. Die Reihenlänge entspricht der Länge des Eingabearrays. Sie wird hauptsächlich für Diagramme verwendet.
* `right_rsquare`: r-Quadrat der Linie auf der rechten Seite des Splits, siehe [series_fit_line()](series-fit-linefunction.md)
* `right_slope`: Neigung der rechten angenäherten Linie (dies ist eine von y=ax+b)
* `right_interception`: Abfangen der angenäherten linken Linie (dies ist b von y=ax+b)
* `right_variance`: Varianz der Eingabedaten auf der rechten Seite der Teilung
* `right_rvariance`: Restabweichung der Eingangsdaten auf der rechten Seite der Teilung
* `left_rsquare`: r-Quadrat der Linie auf der linken Seite des Splits, siehe [series_fit_line()](series-fit-linefunction.md)
* `left_slope`: Neigung der linken angenäherten Linie (dies ist eine von y=ax+b)
* `left_interception`: Abfangen der angenäherten linken Linie (dies ist b von y=ax+b)
* `left_variance`: Varianz der Eingabedaten auf der linken Seite der Teilung
* `left_rvariance`: Restabweichung der Eingangsdaten auf der linken Seite der Teilung

*Beachten Sie,* dass diese Funktion mehrere Spalten zurückgibt, daher kann sie nicht als Argument für eine andere Funktion verwendet werden.

**Syntax**

Projekt `series_fit_2lines(` *x*`)`
* Gibt alle oben genannten Spalten mit den folgenden Namen zurück: series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx usw.
Projekt (rs, si,`series_fit_2lines(`v)=*x*`)`
* Gibt die folgenden Spalten zurück: rs (r-quadratisch), si (Split-Index), v (Varianz) und der Rest sieht aus`series_fit_2lines(`wie series_fit_2lines_x_rvariance, series_fit_2lines_x_line_fit und etc. erweitern (rs, si, v)=*x*`)`
* Gibt nur Folgendes zurück: rs (r-square), si (split index) und v (variance).
  
**Argumente**

* *x*: Dynamisches Array numerischer Werte.  

> [!TIP]
> Der bequemste Weg, diese Funktion zu verwenden, ist die Anwendung auf die Ergebnisse des [Make-Series-Operators.](make-seriesoperator.md)

**Beispiele**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/samples/series-fit-2lines.png" alt-text="Serie passen 2 Linien":::
