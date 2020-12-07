---
title: series_fit_poly_fl ()-Azure Daten-Explorer
description: In diesem Artikel wird die benutzerdefinierte Funktion "series_fit_poly_fl ()" in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 454aa19c5b38eabce49fd319891772746335ceab
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321827"
---
# <a name="series_fit_poly_fl"></a>series_fit_poly_fl()

Die-Funktion `series_fit_poly_fl()` wendet eine polynomale Regression auf eine Reihe an. Diese Funktion verwendet eine Tabelle, die mehrere Reihen (dynamische numerische Arrays) enthält, und generiert die beste Anpassung mit hoher Reihenfolge für jede Reihe mithilfe der [polynomialen Regression](https://en.wikipedia.org/wiki/Polynomial_regression). Diese Funktion gibt sowohl die Polynomen als auch das interpolierte Polynoms über den Bereich der Reihe zurück.

> [!NOTE]
> Verwenden Sie die native Funktion [series_fit_poly ()](../query/series-fit-poly-function.md). Die folgende Funktion dient nur zu Referenzzwecken.


> [!NOTE]
> * `series_fit_poly_fl()` ist eine [UDF (benutzerdefinierte Funktion)](../query/functions/user-defined-functions.md).
> * Diese Funktion enthält Inline-python und erfordert [die Aktivierung des python ()-Plug](../query/pythonplugin.md#enable-the-plugin) -ins auf dem Cluster. Weitere Informationen finden Sie unter [Verwendung](#usage).
> * Verwenden Sie die systemeigene Funktion [series_fit_line ()](../query/series-fit-linefunction.md)für die lineare Regression einer Reihe mit gleichmäßiger Distanz, die durch den [Operator der Make-Reihe](../query/make-seriesoperator.md)erstellt wurde.

## <a name="syntax"></a>Syntax

`T | invoke series_fit_poly_fl(`*y_series* `,` *y_fit_series* `,` *fit_coeff* `,` *Grad* `, [` *x_series* `,` *x_istime*]`)`
  
## <a name="arguments"></a>Argumente

* *y_series*: der Name der Eingabe Tabellenspalte, die die [abhängige Variable](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)enthält. Das heißt, die Reihe muss angepasst werden.
* *y_fit_series*: der Name der Spalte, in der die am besten geeignete Reihenfolge gespeichert werden soll.
* *fit_coeff*: der Name der Spalte zum Speichern der am besten geeigneten polynomialen Koeffizienten.
* *Grad*: die erforderliche Reihenfolge der Polynomen, die angepasst werden soll. Beispiel: 1 für lineare Regression, 2 für quadratische Regression usw.
* *x_series*: der Name der Spalte, die die [unabhängige Variable](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)enthält, d. h. die x-oder Time-Achse. Dieser Parameter ist optional und wird nur für eine [Reihe ungleichmäßiger Abstände](https://en.wikipedia.org/wiki/Unevenly_spaced_time_series)benötigt. Der Standardwert ist eine leere Zeichenfolge, da x für die Regression einer gleichmäßig abgehaltenen Reihe redundant ist.
* *x_istime*: Dieser boolesche Parameter ist optional. Dieser Parameter ist nur erforderlich, wenn *x_series* angegeben wird und es sich um einen Vektor von DateTime handelt.

## <a name="usage"></a>Verwendung

`series_fit_poly_fl()` ist eine [tabellarische Funktion](../query/functions/user-defined-functions.md#tabular-function)einer benutzerdefinierten Funktion, die mit dem [Aufruf Operator](../query/invokeoperator.md)angewendet werden soll. Sie können den Code entweder in die Abfrage einbetten oder in der Datenbank installieren. Es gibt zwei Verwendungs Optionen: Ad-hoc und persistente Verwendung. Beispiele finden Sie auf den folgenden Registerkarten.

# <a name="ad-hoc"></a>[Ad-hoc](#tab/adhoc)

Fügen Sie den Code für die Ad-hoc-Verwendung mithilfe der [Let-Anweisung](../query/letstatement.md)ein. Es ist keine Berechtigung erforderlich.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_fit_poly_fl=(tbl:(*), y_series:string, y_fit_series:string, fit_coeff:string, degree:int, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_coeff', fit_coeff, 'degree', degree, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_coeff = kargs["fit_coeff"]\n'
        'degree = kargs["degree"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'def fit(ts_row, x_col, y_col, deg):\n'
        '    y = ts_row[y_col]\n'
        '    if x_col == "": # If there is no x column creates sequential range [1, len(y)]\n'
        '       x = np.arange(len(y)) + 1\n'
        '    else: # if x column exists check whether its a time column. If so, normalize it to the [1, len(y)] range, else take it as is.\n'
        '       if x_istime: \n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))\n'
        '           x = x - x.min()\n'
        '           x = x / x.max()\n'
        '           x = x * (len(x) - 1) + 1\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    coeff = np.polyfit(x, y, deg)\n'
        '    p = np.poly1d(coeff)\n'
        '    z = p(x)\n'
        '    return z, coeff\n'
        '\n'
        'result = df\n'
        'if len(df):\n'
        '   result[[y_fit_series, fit_coeff]] = df.apply(fit, axis=1, args=(x_series, y_series, degree,), result_type="expand")\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
};
//
// Fit fifth order polynomial to a regular (evenly spaced) time series, created with make-series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null), fnum1 = dynamic(null), coeff1=dynamic(null)
| invoke series_fit_poly_fl('num', 'fnum', 'coeff', 5)
| render timechart with(ycolumns=num, fnum)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Verwenden Sie für persistente Verwendung [`.create function`](../management/create-function.md) .  Zum Erstellen einer Funktion ist die [Berechtigung Datenbankbenutzer](../management/access-control/role-based-authorization.md)erforderlich.

### <a name="one-time-installation"></a>Einmalige Installation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Fit a polynomial of a specified degree to a series")
series_fit_poly_fl(tbl:(*), y_series:string, y_fit_series:string, fit_coeff:string, degree:int, x_series:string='', x_istime:bool=false)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_coeff', fit_coeff, 'degree', degree, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_coeff = kargs["fit_coeff"]\n'
        'degree = kargs["degree"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'def fit(ts_row, x_col, y_col, deg):\n'
        '    y = ts_row[y_col]\n'
        '    if x_col == "": # If there is no x column creates sequential range [1, len(y)]\n'
        '       x = np.arange(len(y)) + 1\n'
        '    else: # if x column exists check whether its a time column. If so, normalize it to the [1, len(y)] range, else take it as is.\n'
        '       if x_istime: \n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))\n'
        '           x = x - x.min()\n'
        '           x = x / x.max()\n'
        '           x = x * (len(x) - 1) + 1\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    coeff = np.polyfit(x, y, deg)\n'
        '    p = np.poly1d(coeff)\n'
        '    z = p(x)\n'
        '    return z, coeff\n'
        '\n'
        'result = df\n'
        'if len(df):\n'
        '   result[[y_fit_series, fit_coeff]] = df.apply(fit, axis=1, args=(x_series, y_series, degree,), result_type="expand")\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>Verwendung

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Fit fifth order polynomial to a regular (evenly spaced) time series, created with make-series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null), fnum1 = dynamic(null), coeff1=dynamic(null)
| invoke series_fit_poly_fl('num', 'fnum', 'coeff', 5)
| render timechart with(ycolumns=num, fnum)
```

---

:::image type="content" source="images/series-fit-poly-fl/usage-example.png" alt-text="Diagramm mit der fünften Reihenfolge Polynoms, die an eine reguläre Zeitreihe angepasst ist" border="false":::

## <a name="additional-examples"></a>Weitere Beispiele

In den folgenden Beispielen wird davon ausgegangen, dass die Funktion bereits installiert ist:

1. Testen von unregelmäßigen Zeitreihen (nicht gleichmäßig verteilt)
    
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let max_t = datetime(2016-09-03);
    demo_make_series1
    | where TimeStamp between ((max_t-2d)..max_t)
    | summarize num=count() by bin(TimeStamp, 5m), OsVer
    | order by TimeStamp asc
    | where hourofday(TimeStamp) % 6 != 0   //  delete every 6th hour to create unevenly spaced time series
    | summarize TimeStamp=make_list(TimeStamp), num=make_list(num) by OsVer
    | extend fnum = dynamic(null), coeff=dynamic(null)
    | invoke series_fit_poly_fl('num', 'fnum', 'coeff', 8, 'TimeStamp', True)
    | render timechart with(ycolumns=num, fnum)
    ```
    
    :::image type="content" source="images/series-fit-poly-fl/irregular-time-series.png" alt-text="Diagramm mit der achten Reihenfolge Polynoms an eine unregelmäßige Zeitreihe anpassen" border="false":::

1. Fünfte Reihenfolge Polynoms mit Rauschen für x & y-Achsen

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    range x from 1 to 200 step 1
    | project x = rand()*5 - 2.3
    | extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
    | extend y = y + (rand() - 0.5)*0.5*y
    | summarize x=make_list(x), y=make_list(y)
    | extend y_fit = dynamic(null), coeff=dynamic(null)
    | invoke series_fit_poly_fl('y', 'y_fit', 'coeff', 5, 'x')
    |fork (project-away coeff) (project coeff | mv-expand coeff)
    | render linechart
    ```
        
    :::image type="content" source="images/series-fit-poly-fl/fifth-order-noise.png" alt-text="Diagramm der Anpassung der fünften Reihenfolge Polynoms mit Rauschen für x & y-Achsen":::
       
    :::image type="content" source="images/series-fit-poly-fl/fifth-order-noise-table.png" alt-text="Koeffizienten der Eignung der fünften Reihenfolge Polynoms mit Rauschen" border="false":::
