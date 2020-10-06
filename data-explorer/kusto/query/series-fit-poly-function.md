---
title: series_fit_poly ()-Azure Daten-Explorer
description: In diesem Artikel werden die series_fit_poly () in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/21/2020
ms.openlocfilehash: 68f9f55b1ff0c66bb5d50e624f9063d7e177f50a
ms.sourcegitcommit: d0f8d71261f8f01e7676abc77283f87fc450c7b1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91771789"
---
# <a name="series_fit_poly"></a>series_fit_poly ()

Wendet eine polynomale Regression aus einer unabhängigen Variablen (x_series) auf eine abhängige Variable (y_series) an. Diese Funktion verwendet eine Tabelle, die mehrere Reihen (dynamische numerische Arrays) enthält, und generiert die beste Anpassung mit hoher Reihenfolge für jede Reihe mithilfe der [polynomialen Regression](https://en.wikipedia.org/wiki/Polynomial_regression). 

> [!TIP]
> * Verwenden Sie für die lineare Regression einer Reihe mit gleichmäßiger Distanz, die durch den [Operator der Make-Reihe](make-seriesoperator.md)erstellt wurde, die einfachere Funktion [series_fit_line ()](series-fit-linefunction.md). Siehe [Beispiel 2](#example-2).
> * Wenn *x_series* angegeben wird und die Regression für einen hohen Grad erreicht wird, sollten Sie die Normalisierung in den Bereich [0-1] in Erwägung gezogen. Siehe [Beispiel 3](#example-3).
> * Wenn *x_series* vom DateTime-Typ ist, muss es in Double und Normalized konvertiert werden. Siehe [Beispiel 3](#example-3).
> * Eine Referenz Implementierung der polynomialen Regression mithilfe von Inline-python finden Sie unter [series_fit_poly_fl ()](../functions-library/series-fit-poly-fl.md).


## <a name="syntax"></a>Syntax

`T | extend  series_fit_poly(`*y_series* `, ` *x_series* `, ` *Grad*`)`
  
## <a name="arguments"></a>Argumente

|Argument| Beschreibung| Erforderlich/optional| Notizen|
|---|---|---|---|
| *y_series* | Dynamisches numerisches Array, das die [abhängige Variable](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)enthält. | Erforderlich |
| *x_series* | Dynamisches numerisches Array, das die [unabhängige Variable](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)enthält. | Optional. Nur für [nicht gleichmäßig gestafferte Reihen](https://en.wikipedia.org/wiki/Unevenly_spaced_time_series)erforderlich. | Wenn nicht angegeben, wird der Standardwert [1, 2,..., length (y_series)] festgelegt.|
| *theits* | Die erforderliche Reihenfolge der Polynomen, die angepasst werden soll. Beispiel: 1 für lineare Regression, 2 für quadratische Regression usw. | Optional | Der Standardwert ist 1 (lineare Regression).|

## <a name="returns"></a>Gibt zurück

Die- `series_fit_poly()` Funktion gibt die folgenden Spalten zurück:

* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) ist ein Standardmeasure der fit-Qualität. Der Wert ist eine Zahl im Bereich [0-1], wobei 1 der bestmögliche Wert ist, und 0 bedeutet, dass die Daten unsortiert sind und nicht in eine beliebige Zeile passen.
* `coefficients`: Numerisches Array mit den Koeffizienten der am besten angepassten Polynoms mit dem angegebenen Grad, geordnet nach dem höchsten Strom Koeffizienten zum niedrigsten.
* `variance`: Varianz der abhängigen Variablen (y_series).
* `rvariance`: Verbleibende Varianz, bei der es sich um die Varianz zwischen den Eingabedaten Werten handelt
* `poly_fit`: Numerisches Array, das eine Reihe von Werten der am besten angepassten Polynomial enthält. Die Reihen Länge ist gleich der Länge der abhängigen Variablen (y_series). Der Wert, der für die Diagramm Erstellung verwendet wird.

## <a name="examples"></a>Beispiele

### <a name="example-1"></a>Beispiel 1

Ein polynommial der fünften Ordnung mit Rauschen für x & y-Achsen:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 200 step 1
| project x = rand()*5 - 2.3
| extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
| extend y = y + (rand() - 0.5)*0.5*y
| summarize x=make_list(x), y=make_list(y)
| extend series_fit_poly(y, x, 5)
| project-rename fy=series_fit_poly_y_poly_fit, coeff=series_fit_poly_y_coefficients
|fork (project x, y, fy) (project-away x, y, fy)
| render linechart 
```

:::image type="content" source="images/series-fit-poly-function/fifth-order-noise-1.png" alt-text="Diagramm mit der fünften Reihenfolge Polynoms an eine Reihe mit Rauschen anpassen":::

:::image type="content" source="images/series-fit-poly-function/fifth-order-noise-table-1.png" alt-text="Diagramm mit der fünften Reihenfolge Polynoms an eine Reihe mit Rauschen anpassen" border="false":::

### <a name="example-2"></a>Beispiel 2

Vergewissern Sie sich, dass `series_fit_poly` mit Grad = 1 übereinstimmt `series_fit_line` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_series1
| extend series_fit_line(y)
| extend series_fit_poly(y)
| project-rename y_line = series_fit_line_y_line_fit, y_poly = series_fit_poly_y_poly_fit
| fork (project x, y, y_line, y_poly) (project-away id, x, y, y_line, y_poly) 
| render linechart with(xcolumn=x, ycolumns=y, y_line, y_poly)
```

:::image type="content" source="images/series-fit-poly-function/fit-poly-line.png" alt-text="Diagramm mit der fünften Reihenfolge Polynoms an eine Reihe mit Rauschen anpassen":::

:::image type="content" source="images/series-fit-poly-function/fit-poly-line-table.png" alt-text="Diagramm mit der fünften Reihenfolge Polynoms an eine Reihe mit Rauschen anpassen" border="false":::
    
### <a name="example-3"></a>Beispiel 3

Unregelmäßige (nicht gleichmäßig entfernte) Zeitreihe:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  x-axis must be normalized to the range [0-1] if either degree is relatively big (>= 5) or original x range is big.
//  so if x is a time axis it must be normalized as conversion of timestamp to long generate huge numbers (number of 100 nano-sec ticks from 1/1/1970)
//
//  Normalization: x_norm = (x - min(x))/(max(x) - min(x))
//
irregular_ts
| extend series_stats(series_add(TimeStamp, 0))                                                                 //  extract min/max of time axis as doubles
| extend x = series_divide(series_subtract(TimeStamp, series_stats__min), series_stats__max-series_stats__min)  // normalize time axis to [0-1] range
| extend series_fit_poly(num, x, 8)
| project-rename fnum=series_fit_poly_num_poly_fit
| render timechart with(ycolumns=num, fnum)
```
:::image type="content" source="images/series-fit-poly-function/irregular-time-series-1.png" alt-text="Diagramm mit der fünften Reihenfolge Polynoms an eine Reihe mit Rauschen anpassen":::
