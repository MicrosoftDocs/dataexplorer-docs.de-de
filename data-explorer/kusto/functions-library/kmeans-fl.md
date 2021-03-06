---
title: kmeans_fl ()-Azure Daten-Explorer
description: In diesem Artikel wird die benutzerdefinierte Funktion "kmeans_fl ()" in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 10/18/2020
ms.openlocfilehash: 7d2482c36c0c55c34adbc664a6c24f64bdadf7a1
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321946"
---
# <a name="kmeans_fl"></a>kmeans_fl()

Die Funktion `kmeans_fl()` gruppiert ein DataSet mit dem [k-means-Algorithmus](https://en.wikipedia.org/wiki/K-means_clustering).

> [!NOTE]
> * `kmeans_fl()` ist eine [UDF (benutzerdefinierte Funktion)](../query/functions/user-defined-functions.md).
> * Diese Funktion enthält Inline-python und erfordert [die Aktivierung des python ()-Plug](../query/pythonplugin.md#enable-the-plugin) -ins auf dem Cluster. Weitere Informationen finden Sie unter [Verwendung](#usage).

## <a name="syntax"></a>Syntax

`T | invoke kmeans_fl(`*k* `,` *features_cols* `,` *cluster_col*`)`

## <a name="arguments"></a>Argumente

* *k*: die erforderliche Anzahl von Clustern.
* *features_cols*: dynamisches Array, das die Namen der Features-Spalten enthält, die für das Clustering verwendet werden.
* *cluster_col*: der Name der Spalte, in der die Ausgabe Cluster-ID für jeden Datensatz gespeichert wird.

## <a name="usage"></a>Verwendung

`kmeans_fl()` ist eine benutzerdefinierte [Tabellen Funktion](../query/functions/user-defined-functions.md#tabular-function) , die mit dem [Aufruf Operator](../query/invokeoperator.md)angewendet werden soll. Sie können den Code entweder in die Abfrage einbetten oder in der Datenbank installieren. Es gibt zwei Verwendungs Optionen: Ad-hoc und persistente Verwendung. Beispiele finden Sie auf den folgenden Registerkarten.

# <a name="ad-hoc"></a>[Ad-hoc](#tab/adhoc)

Für die Ad-hoc-Verwendung Betten Sie den Code mithilfe der [Let-Anweisung](../query/letstatement.md)ein. Es ist keine Berechtigung erforderlich.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let kmeans_fl=(tbl:(*), k:int, features:dynamic, cluster_col:string)
{
    let kwargs = pack('k', k, 'features', features, 'cluster_col', cluster_col);
    let code =
        '\n'
        'from sklearn.cluster import KMeans\n'
        '\n'
        'k = kargs["k"]\n'
        'features = kargs["features"]\n'
        'cluster_col = kargs["cluster_col"]\n'
        '\n'
        'km = KMeans(n_clusters=k)\n'
        'df1 = df[features]\n'
        'km.fit(df1)\n'
        'result = df\n'
        'result[cluster_col] = km.labels_\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
};
//
// Clusterize room occupancy from sensors measurements.
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature, Humidity, Light, and CO2.
//
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| sample 10
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Verwenden Sie für persistente Verwendung [`.create function`](../management/create-function.md) . Zum Erstellen einer Funktion ist die [Berechtigung Datenbankbenutzer](../management/access-control/role-based-authorization.md)erforderlich.

### <a name="one-time-installation"></a>Einmalige Installation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\ML", docstring = "K-Means clustering")
kmeans_fl(tbl:(*), k:int, features:dynamic, cluster_col:string)
{
    let kwargs = pack('k', k, 'features', features, 'cluster_col', cluster_col);
    let code =
        '\n'
        'from sklearn.cluster import KMeans\n'
        '\n'
        'k = kargs["k"]\n'
        'features = kargs["features"]\n'
        'cluster_col = kargs["cluster_col"]\n'
        '\n'
        'km = KMeans(n_clusters=k)\n'
        'df1 = df[features]\n'
        'km.fit(df1)\n'
        'result = df\n'
        'result[cluster_col] = km.labels_\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>Verwendung

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Clusterize room occupancy from sensors measurements.
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature, Humidity, Light, and CO2.
//
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| sample 10
```

---

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
Timestamp                   Temperature Humidity    Light  CO2         HumidityRatio Occupancy Test  cluster_id
2015-02-02 14:38:00.0000000 23.64       27.1        473    908.8       0.00489763    TRUE      TRUE  1
2015-02-03 01:47:00.0000000 20.575      22.125      0      446         0.00330878    FALSE     TRUE  0
2015-02-10 08:47:00.0000000 20.42666667 33.56       405    494.3333333 0.004986493   TRUE      FALSE 4
2015-02-10 09:15:00.0000000 20.85666667 35.09666667 433    665.3333333 0.005358055   TRUE      FALSE 4
2015-02-11 16:13:00.0000000 21.89       30.0225     429    771.75      0.004879358   TRUE      TRUE  4
2015-02-13 14:06:00.0000000 23.4175     26.5225     608    599.75      0.004728116   TRUE      TRUE  4
2015-02-13 23:09:00.0000000 20.13333333 32.2        0      502.6666667 0.004696278   FALSE     TRUE  0
2015-02-15 18:30:00.0000000 20.5        32.79       0      666.5       0.004893459   FALSE     TRUE  3
2015-02-17 13:43:00.0000000 21.7        33.9        454    1167        0.005450924   TRUE      TRUE  1
2015-02-17 18:17:00.0000000 22.025      34.2225     0      1538.25     0.005614538   FALSE     TRUE  2
```

Extrahieren Sie die Schwerpunkte und die Größe der einzelnen Cluster mit der bereits installierten Funktion:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| summarize Temperature=avg(Temperature), Humidity=avg(Humidity), Light=avg(Light), CO2=avg(CO2), HumidityRatio=avg(HumidityRatio), num=count() by cluster_id
| order by num

```

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
cluster_id  Temperature        Humidity            Light            CO2                HumidityRatio        num
0           20.3507186863278   27.1521395395781    10.1995789883291 486.804272186974   0.00400132147662714  11124
4           20.9247315268427   28.7971160082823    20.7311894656536 748.965771574469   0.00440412568299058  3063
1           22.0284137970445   27.8953334469889    481.872136037748 1020.70779349773   0.00456692559904535  2514
3           22.0344177115763   25.1151053429273    462.358969056434 656.310608696507   0.00411782436443015  2176
2           21.4091216082295   31.8363099552939    174.614816229606 1482.05062388414   0.00504573022875817  1683
```
