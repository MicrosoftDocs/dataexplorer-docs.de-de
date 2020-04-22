---
title: series_fit_line() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_fit_line() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 9b36dc51dded4de7e5b57145b918e6741ab8757d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663481"
---
# <a name="series_fit_line"></a>series_fit_line()

Wendet die lineare Regression auf eine Reihe an und gibt mehrere Spalten zurück.  

Nimmt einen Ausdruck, der dynamisches numerisches Array enthält, als Eingabe und führt eine [lineare Regression aus,](https://en.wikipedia.org/wiki/Line_fitting) um die Linie zu finden, die am besten zu ihr passt. Diese Funktion sollte für Zeitreihenarrays, die der Ausgabe des Operators „make-series“ entsprechen, verwendet werden. Es werden die folgenden Spalten generiert:
* `rsquare`: [r-square](https://en.wikipedia.org/wiki/Coefficient_of_determination) ist ein Standardmaß für die Passformqualität. Es ist eine Zahl im Bereich [0-1], wobei 1 die beste Option ist und 0 bedeutet, dass die Daten unsortiert sind und nicht in jede beliebige Zeile passen. 
* `slope`: Neigung der angenäherten Linie (dies ist eine von y=ax+b)
* `variance`: Varianz der Eingabedaten
* `rvariance`: Restabweichung, d. h. die Varianz zwischen den Eingabedatenwerten der angenäherten Werte.
* `interception`: Abfangen der angenäherten Zeile (dies ist b von y=ax+b)
* `line_fit`: numerisches Array mit einer Reihe von Werten der am besten angepassten Linie. Die Reihenlänge entspricht der Länge des Eingabearrays. Sie wird hauptsächlich für Diagramme verwendet.

**Syntax**

`series_fit_line(`*X*`)`

**Argumente**

* *x*: Dynamisches Array numerischer Werte.

> [!TIP]
> Der bequemste Weg, diese Funktion zu verwenden, ist die Anwendung auf die Ergebnisse des [Make-Series-Operators.](make-seriesoperator.md)

**Beispiele**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(y)
| render timechart
```

:::image type="content" source="images/samples/series-fit-line.png" alt-text="Serie fit Linie":::

| RSquare | Slope | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064, 3.7945, 6.526, 9.256, 11.987, 14.718, 17.449, 20.180, 22.910, 25.641, 28.371, 31.102 |