---
title: series_fit_line_dynamic ()-Azure Daten-Explorer
description: In diesem Artikel wird series_fit_line_dynamic () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 80b54dce81799304a4297ee1192f2ee1475d2ec2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351521"
---
# <a name="series_fit_line_dynamic"></a>series_fit_line_dynamic()

Wendet die lineare Regression für eine Reihe an und gibt ein dynamisches Objekt zurück.  

Nimmt einen Ausdruck, der das dynamische numerische Array enthält, als Eingabe und führt eine [lineare Regression](https://en.wikipedia.org/wiki/Line_fitting) aus, um die am besten geeignete Zeile zu finden. Diese Funktion sollte für Zeitreihenarrays, die der Ausgabe des Operators „make-series“ entsprechen, verwendet werden. Es wird ein dynamischer Wert mit folgendem Inhalt generiert:
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) ist ein Standardmeasure der fit-Qualität. Dabei handelt es sich um eine Zahl im Bereich [0-1], wobei 1 die bestmögliche Anpassung ist, und 0 bedeutet, dass die Daten nicht geordnet sind und nicht in eine beliebige Zeile passen.
* `slope`: Steigung der ungefächelten Linie (der *a*-Wert von *y = ax + b*)
* `variance`: Varianz der Eingabedaten
* `rvariance`: Verbleibende Varianz, bei der es sich um die Abweichung zwischen den Eingabedaten Werten und den Näherungs Werten handelt.
* `interception`: Abfangen der näherenden Zeile (der *b*-Wert von *y = ax + b*)
* `line_fit`: Numerisches Array, das eine Reihe von Werten der am besten geeigneten Linie enthält. Die Reihenlänge entspricht der Länge des Eingabearrays. Es wird hauptsächlich zum Charting verwendet.

Dieser Operator ähnelt [series_fit_line](series-fit-linefunction.md), aber im Gegensatz `series-fit-line` dazu wird ein dynamischer Behälter zurückgegeben.

## <a name="syntax"></a>Syntax

`series_fit_line_dynamic(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: dynamisches Array numerischer Werte.

> [!TIP]
> Die einfachste Methode, diese Funktion zu verwenden, besteht darin, Sie auf die Ergebnisse des Operators " [make-Series](make-seriesoperator.md) " anzuwenden.

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend fit=series_fit_line_dynamic(y)
| extend RSquare=fit.rsquare, Slope=fit.slope, Variance=fit.variance,RVariance=fit.rvariance,Interception=fit.interception,LineFit=fit.line_fit
| render timechart
```
 
:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="Linie für Reihen Anpassung":::

| RSquare | Steigung | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1,064, 3,7945, 6,526, 9,256, 11,987, 14,718, 17,449, 20,180, 22,910, 25,641, 28,371, 31,102 |
 
