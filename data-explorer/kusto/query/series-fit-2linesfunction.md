---
title: series_fit_2lines ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird series_fit_2lines () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 6c16b535962271a7aaf4acad63f52da028b49e33
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618739"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

Wendet die lineare Regression zweier Segmente auf eine Reihe an und gibt mehrere Spalten zurück.  

Nimmt einen Ausdruck, der das dynamische numerische Array enthält, als Eingabe an und wendet die [lineare Regression zweier Segmente](https://en.wikipedia.org/wiki/Segmented_regression) an, um Trend Änderungen in einer Reihe zu identifizieren und zu quantifizieren. Die-Funktion durchläuft die Reihen Indizes, und in jeder Iteration wird die Reihe auf zwei Teile aufgeteilt, eine separate Zeile (mithilfe von [series_fit_line ()](series-fit-linefunction.md)) an jeden Teil und das gesamte r-Quadrat berechnen. Die beste Teilung ist diejenige, die „r-square“ maximiert hat. Die Funktion gibt die dazugehörigen Parameter zurück:
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) ist ein Standardmeasure der fit-Qualität. Es ist eine Zahl im Bereich [0-1], wobei 1 die beste Option ist und 0 bedeutet, dass die Daten unsortiert sind und nicht in jede beliebige Zeile passen.
* `split_idx`: der Index des Breakpoints zu 2 Segmenten (null basiert)
* `variance`: Varianz der Eingabedaten
* `rvariance`: verbleibende Varianz, bei der es sich um die Abweichung zwischen den Eingabedaten Werten (durch die zwei Liniensegmente) handelt.
* `line_fit`: numerisches Array, das eine Reihe von Werten der am besten angepassten Linie enthält. Die Reihenlänge entspricht der Länge des Eingabearrays. Sie wird hauptsächlich für Diagramme verwendet.
* `right_rsquare`: r-Quadrat der Zeile auf der rechten Seite der Teilung, siehe [series_fit_line ()](series-fit-linefunction.md)
* `right_slope`: Steigung der rechten Näherungs Linie (Dies ist ein von y = ax + b)
* `right_interception`: Abfangen der näherenden linken Linie (Dies ist b von y = ax + b)
* `right_variance`: Varianz der Eingabedaten auf der rechten Seite der Teilung
* `right_rvariance`: verbleibende Varianz der Eingabedaten auf der rechten Seite der Teilung
* `left_rsquare`: r-Quadrat der Zeile auf der linken Seite der Teilung, siehe [series_fit_line ()](series-fit-linefunction.md)
* `left_slope`: Steigung der linken angleichen Zeile (Dies ist ein von y = ax + b)
* `left_interception`: Abfangen der näherenden linken Linie (Dies ist b von y = ax + b)
* `left_variance`: Varianz der Eingabedaten auf der linken Seite der Teilung
* `left_rvariance`: verbleibende Varianz der Eingabedaten auf der linken Seite der Teilung

*Beachten Sie* , dass diese Funktion mehrere Spalten zurückgibt und daher nicht als Argument für eine andere Funktion verwendet werden kann.

**Syntax**

Projekt `series_fit_2lines(` *x*`)`
* Gibt alle oben erwähnten Spalten mit den folgenden Namen zurück: series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx und usw.
Project (RS, Si, v) =`series_fit_2lines(`*x*`)`
* Gibt die folgenden Spalten zurück: RS (r-Square), Si (Split Index), v (Varianz) und Rest sehen wie series_fit_2lines_x_rvariance, series_fit_2lines_x_line_fit und usw. erweitern (RS, Si, v) =`series_fit_2lines(`*x* .`)`
* Gibt nur Folgendes zurück: rs (r-square), si (split index) und v (variance).
  
**Argumente**

* *x*: dynamisches Array numerischer Werte.  

> [!TIP]
> Die einfachste Methode, diese Funktion zu verwenden, ist die Anwendung auf die Ergebnisse des Operators " [make-Series](make-seriesoperator.md) ".

**Beispiele**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="Reihen passend 2 Zeilen":::
