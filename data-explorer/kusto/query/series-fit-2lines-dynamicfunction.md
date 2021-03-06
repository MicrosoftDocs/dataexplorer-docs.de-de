---
title: series_fit_2lines_dynamic ()-Azure Daten-Explorer
description: In diesem Artikel wird series_fit_2lines_dynamic () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c16e0918ce45df131490686ce20db4086fddf4c0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242126"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

Wendet die lineare Regression zweier Segmente auf eine Reihe an und gibt ein dynamisches Objekt zurück.  

Nimmt einen Ausdruck, der das dynamische numerische Array enthält, als Eingabe an und wendet die [lineare Regression zweier Segmente](https://en.wikipedia.org/wiki/Segmented_regression) an, um Trend Änderungen in einer Reihe zu identifizieren und zu quantifizieren. Die-Funktion durchläuft die Reihen Indizes. In jeder Iterationen wird die Reihe in zwei Teile unterteilt und mit [series_fit_line ()](series-fit-linefunction.md) oder [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md)in eine separate Zeile passt. Die-Funktion passt die Zeilen auf die einzelnen beiden Teile an und berechnet den gesamten R-Quadrat-Wert. Die beste Teilung ist das, das R-Squared maximiert. Die-Funktion gibt die zugehörigen Parameter im dynamischen Wert mit folgendem Inhalt zurück:

* `rsquare`: [R-Squared](https://en.wikipedia.org/wiki/Coefficient_of_determination) ist ein Standardmeasure der fit-Qualität. Dabei handelt es sich um eine Zahl im Bereich von [0-1], wobei 1 die bestmögliche Anpassung ist, und 0 bedeutet, dass die Daten unsortiert sind und nicht in eine beliebige Zeile passen.
* `split_idx`: der Index des Breakpoints zu zwei Segmenten (null basiert).
* `variance`: Varianz der Eingabedaten.
* `rvariance`: verbleibende Varianz, bei der es sich um die Abweichung zwischen den Eingabedaten Werten handelt, die mit den Näherungs Werten übereinstimmen
* `line_fit`: numerisches Array, das eine Reihe von Werten der am besten angepassten Linie enthält. Die Reihenlänge entspricht der Länge des Eingabearrays. Sie wird zum Charting verwendet.
* `right.rsquare`: r-Quadrat der Zeile auf der rechten Seite der Teilung, siehe [series_fit_line ()](series-fit-linefunction.md) oder [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md).
* `right.slope`: Steigung der rechten Näherungs Linie (in der Form y = ax + b).
* `right.interception`: Abfangen der näherenden linken Zeile (b von y = ax + b).
* `right.variance`: Varianz der Eingabedaten auf der rechten Seite der Teilung.
* `right.rvariance`: verbleibende Varianz der Eingabedaten auf der rechten Seite der Teilung.
* `left.rsquare`: r-Quadrat der Zeile auf der linken Seite der Teilung, siehe [series_fit_line ()]. (Series-fit-linefunction.MD) oder [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md).
* `left.slope`: Steigung der linken Näherungs Linie (in der Form y = ax + b).
* `left.interception`: Abfangen der näherenden linken Zeile (in der Form y = ax + b).
* `left.variance`: Varianz der Eingabedaten auf der linken Seite der Teilung.
* `left.rvariance`: verbleibende Varianz der Eingabedaten auf der linken Seite der Teilung.

Dieser Operator ähnelt [series_fit_2lines](series-fit-2linesfunction.md). Im Gegensatz zu `series-fit-2lines` wird ein dynamischer Behälter zurückgegeben.

## <a name="syntax"></a>Syntax

`series_fit_2lines_dynamic(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: dynamisches Array numerischer Werte.  

> [!TIP]
> Die einfachste Methode, diese Funktion zu verwenden, ist die Anwendung auf die Ergebnisse des Operators " [make-Series](make-seriesoperator.md) ".

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="Reihen passend 2 Zeilen":::
