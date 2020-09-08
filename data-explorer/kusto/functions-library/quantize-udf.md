---
title: quantize_udf ()-Azure Daten-Explorer
description: In diesem Artikel wird die benutzerdefinierte Funktion "quantize_udf ()" in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 19d35e70ddd47b1b0390778a0e775a147235c587
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557858"
---
# <a name="quantize_udf"></a>quantize_udf ()


Die `quantize_udf()` metrikspalten der Funktions Behälter. Metrikspalten werden basierend auf dem K-Means-Algorithmus in kategorische Bezeichnungen quantifizierert.

> [!NOTE]
>* Diese Funktion enthält Inline-python und erfordert [die Aktivierung des python ()-Plug](../query/pythonplugin.md#enable-the-plugin) -ins auf dem Cluster.
>* Diese Funktion ist eine [UDF (benutzerdefinierte Funktion)](../query/functions/user-defined-functions.md). Weitere Informationen finden Sie unter [Verwendung](#usage).

## <a name="syntax"></a>Syntax

`T | invoke quantize_udf(`*num_bins* `,` *in_cols* `,` *out_cols* `,` *Bezeichnungen*`)`

## <a name="arguments"></a>Argumente

* *num_bins*: erforderliche Anzahl von Containern.
* *in_cols*: dynamisches Array, das die Namen der zu quantifizienden Spalten enthält.
* *out_cols*: dynamisches Array, das die Namen der entsprechenden Ausgabespalten für die klassifizierten Werte enthält.
* *Labels*: dynamisches Array mit den Namen der Bezeichnung. Dieser Parameter ist optional. Wenn keine *Bezeichnungen* angegeben werden, werden die Bereiche "bin" verwendet.

## <a name="usage"></a>Verbrauch

* `quantize_udf()` ist eine benutzerdefinierte Funktion. Sie können entweder den Code in die Abfrage einbetten oder ihn in der Datenbank installieren:
    * Fügen Sie den Code für die Ad-hoc-Verwendung mithilfe der [Let-Anweisung](../query/letstatement.md)ein. Es ist keine Berechtigung erforderlich.
    * Verwenden Sie für die wiederholte Verwendung die [. Create-Funktion](../management/create-function.md). <br>
        Zum Erstellen einer Funktion ist die [Berechtigung Datenbankbenutzer](../management/access-control/role-based-authorization.md)erforderlich.
* `quantize_udf()`ist eine [tabellarische Funktion](../query/functions/user-defined-functions.md#tabular-function), die mit dem [Aufruf Operator](../query/invokeoperator.md) angewendet werden soll.

# <a name="ad-hoc-usage"></a>[Ad-hoc-Verwendung](#tab/adhoc)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let qunatize_udf=(tbl:(*), num_bins:int, in_cols:dynamic, out_cols:dynamic, labels:dynamic=dynamic(null))
{
    let kwargs = pack('num_bins', num_bins, 'in_cols', in_cols, 'out_cols', out_cols, 'labels', labels);
    let code =
        '\n'
        'from sklearn.preprocessing import KBinsDiscretizer\n'
        '\n'
        'num_bins = kargs["num_bins"]\n'
        'in_cols = kargs["in_cols"]\n'
        'out_cols = kargs["out_cols"]\n'
        'labels = kargs["labels"]\n'
        '\n'
        'result = df\n'
        'binner = KBinsDiscretizer(n_bins=num_bins, encode="ordinal", strategy="kmeans")\n'
        'df_in = df[in_cols]\n'
        'bdata = binner.fit_transform(df_in)\n'
        'if labels is None:\n'
        '    for i in range(len(out_cols)):    # loop on each column and convert it to binned labels\n'
        '        ii = np.round(binner.bin_edges_[i], 3)\n'
        '        labels = [str(ii[j-1]) + \'-\' + str(ii[j]) for j in range(1, num_bins+1)]\n'
        '        result.loc[:,out_cols[i]] = np.take(labels, bdata[:, i].astype(int))\n'
        'else:\n'
        '    result[out_cols] = np.take(labels, bdata.astype(int))\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
};
//
union 
(range x from 1 to 5 step 1),
(range x from 10 to 15 step 1),
(range x from 20 to 25 step 1)
| extend x_label='', x_bin=''
| invoke qunatize_udf(3, pack_array('x'), pack_array('x_label'), pack_array('Low', 'Med', 'High'))
| invoke qunatize_udf(3, pack_array('x'), pack_array('x_bin'), dynamic(null))
```

# <a name="persistent-usage"></a>[Persistente Verwendung](#tab/persistent)

* **Einmalige Installation**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\ML", docstring = "Binning metric columns")
qunatize_udf(tbl:(*), num_bins:int, in_cols:dynamic, out_cols:dynamic, labels:dynamic)
{
    let kwargs = pack('num_bins', num_bins, 'in_cols', in_cols, 'out_cols', out_cols, 'labels', labels);
    let code =
        '\n'
        'from sklearn.preprocessing import KBinsDiscretizer\n'
        '\n'
        'num_bins = kargs["num_bins"]\n'
        'in_cols = kargs["in_cols"]\n'
        'out_cols = kargs["out_cols"]\n'
        'labels = kargs["labels"]\n'
        '\n'
        'result = df\n'
        'binner = KBinsDiscretizer(n_bins=num_bins, encode="ordinal", strategy="kmeans")\n'
        'df_in = df[in_cols]\n'
        'bdata = binner.fit_transform(df_in)\n'
        'if labels is None:\n'
        '    for i in range(len(out_cols)):    # loop on each column and convert it to binned labels\n'
        '        ii = np.round(binner.bin_edges_[i], 3)\n'
        '        labels = [str(ii[j-1]) + \'-\' + str(ii[j]) for j in range(1, num_bins+1)]\n'
        '        result.loc[:,out_cols[i]] = np.take(labels, bdata[:, i].astype(int))\n'
        'else:\n'
        '    result[out_cols] = np.take(labels, bdata.astype(int))\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

* **Verwendung**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
union 
(range x from 1 to 5 step 1),
(range x from 10 to 15 step 1),
(range x from 20 to 25 step 1)
| extend x_label='', x_bin=''
| invoke qunatize_udf(3, pack_array('x'), pack_array('x_label'), pack_array('Low', 'Med', 'High'))
| invoke qunatize_udf(3, pack_array('x'), pack_array('x_bin'), dynamic(null))
```

---

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
x    x_label    x_bin
1    Low        1.0-7.75
2    Low        1.0-7.75
3    Low        1.0-7.75
4    Low        1.0-7.75
5    Low        1.0-7.75
20   High       17.5-25.0
21   High       17.5-25.0
22   High       17.5-25.0
23   High       17.5-25.0
24   High       17.5-25.0
25   High       17.5-25.0
10   Med        7.75-17.5
11   Med        7.75-17.5
12   Med        7.75-17.5
13   Med        7.75-17.5
14   Med        7.75-17.5
15   Med        7.75-17.5
```
