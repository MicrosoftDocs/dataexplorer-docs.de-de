---
title: series_rolling_fl ()-Azure Daten-Explorer
description: In diesem Artikel wird die benutzerdefinierte Funktion "series_rolling_fl ()" in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: fe9ea2556346edaf0327e0d9ce50d4d4519d4bb3
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321793"
---
# <a name="series_rolling_fl"></a>series_rolling_fl()


Die-Funktion `series_rolling_fl()` wendet eine parallele Aggregation auf eine Reihe an. Sie nimmt eine Tabelle mit mehreren Reihen (dynamisches numerisches Array) und gilt für jede Reihe für eine parallele Aggregations Funktion.

> [!NOTE]
> * `series_rolling_fl()` ist eine [UDF (benutzerdefinierte Funktion)](../query/functions/user-defined-functions.md).
> * Diese Funktion enthält Inline-python und erfordert [die Aktivierung des python ()-Plug](../query/pythonplugin.md#enable-the-plugin) -ins auf dem Cluster. Weitere Informationen finden Sie unter [Verwendung](#usage).

## <a name="syntax"></a>Syntax

`T | invoke series_rolling_fl(`*y_series* `,` *y_rolling_series* `,` *n* `,` *aggr* `,` *aggr_params* `,` *Center*`)`

## <a name="arguments"></a>Argumente

* *y_series*: der Name der Spalte (der Eingabe Tabelle), in der die zu sortierenden Reihen enthalten sind.
* *y_rolling_series*: der Name der Spalte, in der die parallele Aggregations Reihe gespeichert werden soll.
* *n*: die Breite des parallelen Fensters.
* *aggr*: der Name der zu verwendenden Aggregations Funktion. Siehe [Aggregations Funktionen](#aggregation-functions).
* *aggr_params*: optionale Parameter für die Aggregations Funktion.
* *Center*: ein optionaler boolescher Wert, der angibt, ob das parallele Fenster eine der folgenden Optionen ist:
    * wird symmetrisch vor und nach dem aktuellen Punkt angewendet, oder 
    * wird vom aktuellen Punkt rückwärts angewendet. <br>
    Standardmäßig ist *Center* false, um die Berechnung der Streamingdaten zu berechnen.

## <a name="aggregation-functions"></a>Aggregationsfunktionen

Diese Funktion unterstützt jede Aggregations Funktion aus [numpy](https://numpy.org/) oder [scipy. stats](https://docs.scipy.org/doc/scipy/reference/stats.html#module-scipy.stats) , die einen skalaren Wert aus einer Reihe berechnet. Die folgende Liste ist nicht vollständig:

* [`sum`](https://numpy.org/doc/stable/reference/generated/numpy.sum.html#numpy.sum) 
* [`mean`](https://numpy.org/doc/stable/reference/generated/numpy.mean.html?highlight=mean#numpy.mean)
* [`min`](https://numpy.org/doc/stable/reference/generated/numpy.amin.html#numpy.amin)
* [`max`](https://numpy.org/doc/stable/reference/generated/numpy.amax.html)
* [`ptp (max-min)`](https://numpy.org/doc/stable/reference/generated/numpy.ptp.html)
* [`percentile`](https://numpy.org/doc/stable/reference/generated/numpy.percentile.html)
* [`median`](https://numpy.org/doc/stable/reference/generated/numpy.median.html)
* [`std`](https://numpy.org/doc/stable/reference/generated/numpy.std.html)
* [`var`](https://numpy.org/doc/stable/reference/generated/numpy.var.html)
* [`gmean` (geometrischer Mittelwert)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.gmean.html)
* [`hmean` (harmonischer Mittelwert)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.hmean.html)
* [`mode` (häufigste Werte)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mode.html)
* [`moment` (n<sup>.</sup> Moment)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.moment.html)
* [`tmean` (Mittel gekürzt)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmean.html)
* [`tmin`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmin.html) 
* [`tmax`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmax.html)
* [`tstd`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tstd.html)
* [`iqr` (zwischen quantifilbereich)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.iqr.html) 

## <a name="usage"></a>Verwendung

`series_rolling_fl()` ist eine benutzerdefinierte [Tabellen Funktion](../query/functions/user-defined-functions.md#tabular-function), die mit dem [Aufruf Operator](../query/invokeoperator.md)angewendet werden soll. Sie können den Code entweder in die Abfrage einbetten oder in der Datenbank installieren. Es gibt zwei Verwendungs Optionen: Ad-hoc und persistente Verwendung. Beispiele finden Sie auf den folgenden Registerkarten.

# <a name="ad-hoc"></a>[Ad-hoc](#tab/adhoc)

Fügen Sie den Code für die Ad-hoc-Verwendung mithilfe der [Let-Anweisung](../query/letstatement.md)ein. Es ist keine Berechtigung erforderlich.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_rolling_fl = (tbl:(*), y_series:string, y_rolling_series:string, n:int, aggr:string, aggr_params:dynamic=dynamic([null]), center:bool=true)
{
    let kwargs = pack('y_series', y_series, 'y_rolling_series', y_rolling_series, 'n', n, 'aggr', aggr, 'aggr_params', aggr_params, 'center', center);
    let code =
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_rolling_series = kargs["y_rolling_series"]\n'
        'n = kargs["n"]\n'
        'aggr = kargs["aggr"]\n'
        'aggr_params = kargs["aggr_params"]\n'
        'center = kargs["center"]\n'
        'result = df\n'
        'in_s = df[y_series]\n'
        'func = getattr(np, aggr, None)\n'
        'if not func:\n'
        '    import scipy.stats\n'
        '    func = getattr(scipy.stats, aggr)\n'
        'if func:\n'
        '    result[y_rolling_series] = list(pd.Series(in_s[i]).rolling(n, center=center, min_periods=1).apply(func, args=aggr_params).values for i in range(len(in_s)))\n'
        '\n';
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
;
//
//  Calculate rolling median of 9 elements
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend rolling_med = dynamic(null)
| invoke series_rolling_fl('num', 'rolling_med', 9, 'median')
| render timechart
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Verwenden Sie für persistente Verwendung [`.create function`](../management/create-function.md) . Zum Erstellen einer Funktion ist die [Berechtigung Datenbankbenutzer](../management/access-control/role-based-authorization.md)erforderlich.

### <a name="one-time-installation"></a>Einmalige Installation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Rolling window functions on a series")
series_rolling_fl(tbl:(*), y_series:string, y_rolling_series:string, n:int, aggr:string, aggr_params:dynamic, center:bool=true)
{
    let kwargs = pack('y_series', y_series, 'y_rolling_series', y_rolling_series, 'n', n, 'aggr', aggr, 'aggr_params', aggr_params, 'center', center);
    let code =
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_rolling_series = kargs["y_rolling_series"]\n'
        'n = kargs["n"]\n'
        'aggr = kargs["aggr"]\n'
        'aggr_params = kargs["aggr_params"]\n'
        'center = kargs["center"]\n'
        'result = df\n'
        'in_s = df[y_series]\n'
        'func = getattr(np, aggr, None)\n'
        'if not func:\n'
        '    import scipy.stats\n'
        '    func = getattr(scipy.stats, aggr)\n'
        'if func:\n'
        '    result[y_rolling_series] = list(pd.Series(in_s[i]).rolling(n, center=center, min_periods=1).apply(func, args=aggr_params).values for i in range(len(in_s)))\n'
        '\n';
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>Verwendung

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Calculate rolling median of 9 elements
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend rolling_med = dynamic(null)
| invoke series_rolling_fl('num', 'rolling_med', 9, 'median', dynamic([null]))
| render timechart
```

---

:::image type="content" source="images/series-rolling-fl/rolling-median-9.png" alt-text="Diagramm, das den parallelen Median von 9 Elementen darstellt" border="false":::

## <a name="additional-examples"></a>Weitere Beispiele

In den folgenden Beispielen wird davon ausgegangen, dass die Funktion bereits installiert ist:

1. Berechnen der parallelen min., maximalen & 75. Perzentil von 15 Elementen
    
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    //
    //  Calculate rolling min, max & 75th percentile of 15 elements
    //
    demo_make_series1
    | make-series num=count() on TimeStamp step 1h by OsVer
    | extend rolling_min = dynamic(null), rolling_max = dynamic(null), rolling_pct = dynamic(null)
    | invoke series_rolling_fl('num', 'rolling_min', 15, 'min', dynamic([null]))
    | invoke series_rolling_fl('num', 'rolling_max', 15, 'max', dynamic([null]))
    | invoke series_rolling_fl('num', 'rolling_pct', 15, 'percentile', dynamic([75]))
    | render timechart
    ```
    
    :::image type="content" source="images/series-rolling-fl/graph-rolling-15.png" alt-text="Diagramm, das das parallele Minimum, das maximale & 75. Perzentil von 15 Elementen darstellt" border="false":::

1. Berechnen des nicht aufgeschnittenen Mittelwert
        
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    //
    //  Calculate rolling trimmed mean
    //
    range x from 1 to 100 step 1
    | extend y=iff(x % 13 == 0, 2.0, iff(x % 23 == 0, -2.0, rand()))
    | summarize x=make_list(x), y=make_list(y)
    | extend yr = dynamic(null)
    | invoke series_rolling_fl('y', 'yr', 7, 'tmean', pack_array(pack_array(-2, 2), pack_array(false, false))) //  trimmed mean: ignoring values outside [-2,2] inclusive
    | render linechart
    ```
    
    :::image type="content" source="images/series-rolling-fl/rolling-trimmed-mean.png" alt-text="Diagramm mit dem aufgeschnittenen Mittelwert" border="false":::
    
