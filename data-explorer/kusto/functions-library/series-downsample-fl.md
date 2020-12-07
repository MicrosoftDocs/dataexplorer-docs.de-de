---
title: series_downsample_fl ()-Azure Daten-Explorer
description: In diesem Artikel wird die benutzerdefinierte Funktion "series_downsample_fl ()" in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2020
ms.openlocfilehash: 172d866a84e4718e3d95e8fa646187e79bec65f7
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321861"
---
# <a name="series_downsample_fl"></a>series_downsample_fl()


Die Funktion `series_downsample_fl()` [downsamples eine Zeitreihe mit einem ganzzahligen Faktor](https://en.wikipedia.org/wiki/Downsampling_(signal_processing)#Downsampling_by_an_integer_factor). Diese Funktion nimmt eine Tabelle, die mehrere Zeitreihen (dynamisches numerisches Array) enthält, und führt ein downsamples für jede Reihe aus. Die Ausgabe enthält sowohl die gröbere-Reihe als auch das entsprechende Zeit Array. Um [Aliasing](https://en.wikipedia.org/wiki/Aliasing)zu vermeiden, wendet die Funktion vor der Unterstichprobe einen einfachen [Low-Pass-Filter](https://en.wikipedia.org/wiki/Low-pass_filter) auf jede Reihe an.

> [!NOTE]
> Diese Funktion ist eine [UDF (benutzerdefinierte Funktion)](../query/functions/user-defined-functions.md). Weitere Informationen finden Sie unter [Verwendung](#usage).

## <a name="syntax"></a>Syntax

`T | invoke series_downsample_fl(`*t_col* `,` *y_col* `,` *ds_t_col* `,` *ds_y_col* `,` *sampling_factor*`)`

## <a name="arguments"></a>Argumente

* *t_col*: der Name der Spalte (der Eingabe Tabelle), die die Zeitachse der zu downproben enden Reihe enthält.
* *y_col*: der Name der Spalte (der Eingabe Tabelle), die die zu downproben enden Reihen enthält.
* *ds_t_col*: der Name der Spalte, in der die Downsampling-Zeitachse der einzelnen Reihen gespeichert werden soll.
* *ds_y_col*: der Name der Spalte, in der die Stichproben Reihe gespeichert werden soll.
* *sampling_factor*: eine ganze Zahl, die die erforderliche Stichprobenentnahme angibt.

## <a name="usage"></a>Verwendung

`series_downsample_fl()` ist eine benutzerdefinierte [Tabellen Funktion](../query/functions/user-defined-functions.md#tabular-function), die mit dem [Aufruf Operator](../query/invokeoperator.md)angewendet werden soll. Sie können den Code entweder in die Abfrage einbetten oder in der Datenbank installieren. Es gibt zwei Verwendungs Optionen: Ad-hoc und persistente Verwendung. Beispiele finden Sie auf den folgenden Registerkarten.

# <a name="ad-hoc"></a>[Ad-hoc](#tab/adhoc)

Fügen Sie den Code für die Ad-hoc-Verwendung mithilfe der [Let-Anweisung](../query/letstatement.md)ein. Es ist keine Berechtigung erforderlich.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_downsample_fl=(tbl:(*), t_col:string, y_col:string, ds_t_col:string, ds_y_col:string, sampling_factor:int)
{
    tbl
    | extend _t_ = column_ifexists(t_col, dynamic(0)), _y_ = column_ifexists(y_col, dynamic(0))
    | extend _y_ = series_fir(_y_, repeat(1, sampling_factor), true, true)    //  apply a simple low pass filter before sub-sampling
    | mv-apply _t_ to typeof(DateTime), _y_ to typeof(double) on
    (extend rid=row_number()
    | where rid % sampling_factor == (sampling_factor/2+1)                    //  sub-sampling
    | summarize _t_ = make_list(_t_), _y_ = make_list(_y_))
    | extend cols = pack(ds_t_col, _t_, ds_y_col, _y_)
    | project-away _t_, _y_
    | evaluate bag_unpack(cols)
}
;
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| invoke series_downsample_fl('TimeStamp', 'num', 'coarse_TimeStamp', 'coarse_num', 4)
| render timechart with(xcolumn=coarse_TimeStamp, ycolumns=coarse_num)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Verwenden Sie für persistente Verwendung [`.create function`](../management/create-function.md) . Zum Erstellen einer Funktion ist die [Berechtigung Datenbankbenutzer](../management/access-control/role-based-authorization.md)erforderlich.

### <a name="one-time-installation"></a>Einmalige Installation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Downsampling a series by an integer factor")
series_downsample_fl(tbl:(*), t_col:string, y_col:string, ds_t_col:string, ds_y_col:string, sampling_factor:int)
{
    tbl
    | extend _t_ = column_ifexists(t_col, dynamic(0)), _y_ = column_ifexists(y_col, dynamic(0))
    | extend _y_ = series_fir(_y_, repeat(1, sampling_factor), true, true)    //  apply a simple low pass filter before sub-sampling
    | mv-apply _t_ to typeof(DateTime), _y_ to typeof(double) on
    (extend rid=row_number()
    | where rid % sampling_factor == (sampling_factor/2+1)                    //  sub-sampling
    | summarize _t_ = make_list(_t_), _y_ = make_list(_y_))
    | extend cols = pack(ds_t_col, _t_, ds_y_col, _y_)
    | project-away _t_, _y_
    | evaluate bag_unpack(cols)
}
```

### <a name="usage"></a>Verwendung

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| invoke series_downsample_fl('TimeStamp', 'num', 'coarse_TimeStamp', 'coarse_num', 4)
| render timechart with(xcolumn=coarse_TimeStamp, ycolumns=coarse_num)
```

---

Die Zeitreihen, für die ein Verkleinerung durchgeführt wurde: Diagramm, das das :::image type="content" source="images/series-downsample-fl/downsampling-demo.png" alt-text="Verkleinerung einer Zeitreihe anzeigt" border="false":::

Im folgenden finden Sie die ursprüngliche Zeitreihe (vor dem Downsampling):
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| render timechart with(xcolumn=TimeStamp, ycolumns=num)
```

:::image type="content" source="images/series-downsample-fl/original-time-series.png" alt-text="Diagramm mit der ursprünglichen Zeitreihe vor dem Verkleinerung" border="false":::

