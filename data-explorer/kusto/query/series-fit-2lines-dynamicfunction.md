---
title: series_fit_2lines_dynamic ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird series_fit_2lines_dynamic () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e2671b112ff1a73a5f9bbc10d6537a5e8d7b755e
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618767"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

Wendet die lineare Regression zweier Segmente auf eine Reihe an und gibt ein dynamisches Objekt zurück.  

Nimmt einen Ausdruck, der das dynamische numerische Array enthält, als Eingabe an und wendet die [lineare Regression zweier Segmente](https://en.wikipedia.org/wiki/Segmented_regression) an, um Trend Änderungen in einer Reihe zu identifizieren und zu quantifizieren. Die-Funktion durchläuft die Reihen Indizes, und in jeder Iteration wird die Reihe in zwei Teile aufgeteilt. dabei wird eine separate Zeile (mithilfe von [series_fit_line ()](series-fit-linefunction.md) oder [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md)) an jeden Teil und das gesamte r-Quadrat berechnet. Die beste Teilung ist eine, die das "r-Square" maximiert. die-Funktion gibt die zugehörigen Parameter im dynamischen Wert mit folgendem Inhalt zurück::
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) ist ein Standardmeasure der fit-Qualität. Es ist eine Zahl im Bereich [0-1], wobei 1 die beste Option ist und 0 bedeutet, dass die Daten unsortiert sind und nicht in jede beliebige Zeile passen.
* `split_idx`: der Index des Breakpoints zu 2 Segmenten (null basiert)
* `variance`: Varianz der Eingabedaten
* `rvariance`: verbleibende Varianz, bei der es sich um die Abweichung zwischen den Eingabedaten Werten (durch die zwei Liniensegmente) handelt.
* `line_fit`: numerisches Array, das eine Reihe von Werten der am besten angepassten Linie enthält. Die Reihenlänge entspricht der Länge des Eingabearrays. Sie wird hauptsächlich für Diagramme verwendet.
* `right.rsquare`: r-Quadrat der Zeile auf der rechten Seite der Teilung, siehe [series_fit_line ()](series-fit-linefunction.md) oder[series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md)
* `right.slope`: Steigung der rechten Näherungs Linie (Dies ist ein von y = ax + b)
* `right.interception`: Abfangen der näherenden linken Linie (Dies ist b von y = ax + b)
* `right.variance`: Varianz der Eingabedaten auf der rechten Seite der Teilung
* `right.rvariance`: verbleibende Varianz der Eingabedaten auf der rechten Seite der Teilung
* `left.rsquare`: r-Quadrat der Zeile auf der linken Seite der Teilung, siehe [series_fit_line ()](series-fit-linefunction.md) oder [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md)
* `left.slope`: Steigung der linken angleichen Zeile (Dies ist ein von y = ax + b)
* `left.interception`: Abfangen der näherenden linken Linie (Dies ist b von y = ax + b)
* `left.variance`: Varianz der Eingabedaten auf der linken Seite der Teilung
* `left.rvariance`: verbleibende Varianz der Eingabedaten auf der linken Seite der Teilung

Dieser Operator ähnelt [series_fit_2lines](series-fit-2linesfunction.md), aber im Gegensatz `series-fit-2lines` dazu wird ein dynamischer Behälter zurückgegeben.

**Syntax**

`series_fit_2lines_dynamic(`*Stuben*`)`

**Argumente**

* *x*: dynamisches Array numerischer Werte.  

> [!TIP]
> Die einfachste Methode, diese Funktion zu verwenden, ist die Anwendung auf die Ergebnisse des Operators " [make-Series](make-seriesoperator.md) ".

**Beispiele**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="Reihen passend 2 Zeilen":::
