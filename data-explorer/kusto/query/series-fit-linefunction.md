---
title: series_fit_line ()-Azure Daten-Explorer
description: In diesem Artikel wird series_fit_line () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 876eeaa4550a5433354d50dd44fae3920177d335
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372681"
---
# <a name="series_fit_line"></a>series_fit_line()

Wendet die lineare Regression für eine Reihe an und gibt mehrere Spalten zurück.  

Nimmt einen Ausdruck, der das dynamische numerische Array enthält, als Eingabe und führt eine [lineare Regression](https://en.wikipedia.org/wiki/Line_fitting) durch, um die am besten geeignete Zeile zu finden. Diese Funktion sollte für Zeitreihenarrays, die der Ausgabe des Operators „make-series“ entsprechen, verwendet werden. Die folgenden Spalten werden generiert:
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) ist ein Standardmeasure der fit-Qualität. Es ist eine Zahl im Bereich [0-1], wobei 1 die beste Option ist und 0 bedeutet, dass die Daten unsortiert sind und nicht in jede beliebige Zeile passen. 
* `slope`: Steigung der angeschätzten Linie (Dies ist ein von y = ax + b)
* `variance`: Varianz der Eingabedaten
* `rvariance`: verbleibende Varianz, bei der es sich um die Varianz zwischen den Eingabedaten Werten handelt
* `interception`: Abfangen der näherenden Linie (Dies ist b von y = ax + b)
* `line_fit`: numerisches Array, das eine Reihe von Werten der am besten angepassten Linie enthält. Die Reihenlänge entspricht der Länge des Eingabearrays. Sie wird hauptsächlich für Diagramme verwendet.

**Syntax**

`series_fit_line(`*Stuben*`)`

**Argumente**

* *x*: dynamisches Array numerischer Werte.

> [!TIP]
> Die einfachste Methode, diese Funktion zu verwenden, ist die Anwendung auf die Ergebnisse des Operators " [make-Series](make-seriesoperator.md) ".

**Beispiele**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(y)
| render timechart
```

:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="Linie für Reihen Anpassung":::

| RSquare | Slope | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1,064, 3,7945, 6,526, 9,256, 11,987, 14,718, 17,449, 20,180, 22,910, 25,641, 28,371, 31,102 |
