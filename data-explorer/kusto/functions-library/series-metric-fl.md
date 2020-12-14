---
title: series_metric_fl ()-Azure Daten-Explorer
description: In diesem Artikel wird die benutzerdefinierte Funktion "series_metric_fl ()" in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 12/13/2020
ms.openlocfilehash: 2af82906be2cdbffdc69646a3050376e0a1af867
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97389151"
---
# <a name="series_metric_fl"></a>series_metric_fl ()


`series_metric_fl()`Mit der-Funktion werden Zeitreihen von Metriken ausgewählt und abgerufen, die mit dem [Prometheus](https://prometheus.io/) -Überwachungssystem in Azure Daten-Explorer erfasst wurden. Diese Funktion geht davon aus, dass die in Azure gespeicherten Daten Daten-Explorer nach dem [Prometheus-Datenmodell](https://prometheus.io/docs/concepts/data_model/)strukturiert sind. Jeder Datensatz enthält insbesondere Folgendes:
 * timestamp 
 * Metrikname 
 * Metrikwert 
 * ein variabler Satz von Bezeichnungen ( `key:value` Paare)
 
 Prometheus definiert eine Zeitreihe nach dem metriknamen und einem eindeutigen Satz von Bezeichnungen. Sie können Sätze von Zeitreihen mithilfe von " [Prometheus Query Language" (promql)](https://prometheus.io/docs/prometheus/latest/querying/basics/) abrufen, indem Sie den metriknamen und die Zeitreihen Auswahl (eine Reihe von Bezeichnungen) angeben.

> [!NOTE]
> `series_metric_fl()` ist eine [UDF (benutzerdefinierte Funktion)](../query/functions/user-defined-functions.md). Weitere Informationen finden Sie unter [Verwendung](#usage).

## <a name="syntax"></a>Syntax

`T | invoke series_metric_fl(`*timestamp_col* `,` *name_col* `,` *labels_col* `,` *value_col* `,` *metric_name* `,` *labels_selector* `,` *Lookback* `,` *Offset*`)`

## <a name="arguments"></a>Argumente

* *timestamp_col*: der Name der Spalte, die den Zeitstempel enthält.
* *name_col*: der Name der Spalte, die den metriknamen enthält.
* *labels_col*: der Name der Spalte, die das Wörterbuch "Labels" enthält.
* *value_col*: der Name der Spalte, die den Metrikwert enthält.
* *metric_name*: die abzurufende metrikzeitreihe.
* *labels_selector*: Zeitreihen-Selektor Zeichenfolge, [ähnlich wie promql](https://prometheus.io/docs/prometheus/latest/querying/basics/#time-series-selectors). Dies ist eine Zeichenfolge, die eine Liste von `key:value` Paaren enthält, z `'key1:val1,key2:val2'` . b.. Dieser Parameter ist optional, standardmäßig eine leere Zeichenfolge, d. h. keine Filterung. Beachten Sie, dass reguläre Ausdrücke nicht unterstützt werden. 
* *Lookback*: Bereichs Vektor, der abgerufen werden soll, [ähnlich wie promql](https://prometheus.io/docs/prometheus/latest/querying/basics/#range-vector-selectors). Dieser Parameter ist optional, der Standardwert ist 10 Minuten.
* *Offset*: Offset von der aktuellen Zeit zum Abrufen, [ähnlich wie bei promql](https://prometheus.io/docs/prometheus/latest/querying/basics/#offset-modifier). Daten werden von *vor dem Ende (Offset)-Lookback* vor *(Offset)* abgerufen. Dieser Parameter ist optional, der Standardwert ist 0, was bedeutet, dass Daten bis jetzt abgerufen werden ().

## <a name="usage"></a>Verbrauch

`series_metric_fl()` ist eine benutzerdefinierte [Tabellen Funktion](../query/functions/user-defined-functions.md#tabular-function), die mit dem [Aufruf Operator](../query/invokeoperator.md)angewendet werden soll. Sie können den Code entweder in die Abfrage einbetten oder in der Datenbank installieren. Es gibt zwei Verwendungs Optionen: Ad-hoc und persistente Verwendung. Beispiele finden Sie auf den folgenden Registerkarten.

# <a name="ad-hoc"></a>[Ad-hoc](#tab/adhoc)

Fügen Sie den Code für die Ad-hoc-Verwendung mithilfe der [Let-Anweisung](../query/letstatement.md)ein. Es ist keine Berechtigung erforderlich.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_metric_fl=(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(strcat('"', replace('([:,])','"\\1"', labels_selector), '"'), ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | project timestamp, value, name, labels
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1,host:aks-agentpool-88086459-vmss000001', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Verwenden Sie für persistente Verwendung [`.create function`](../management/create-function.md) . Zum Erstellen einer Funktion ist die [Berechtigung Datenbankbenutzer](../management/access-control/role-based-authorization.md)erforderlich.

### <a name="one-time-installation"></a>Einmalige Installation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\Series", docstring = "Selecting & retrieving metrics like PromQL")
series_metric_fl(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(strcat('"', replace('([:,])','"\\1"', labels_selector), '"'), ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | project timestamp, value, name, labels
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
```

### <a name="usage"></a>Verbrauch

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1,host:aks-agentpool-88086459-vmss000001', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-metric-fl/disk-write-metric-10m.png" alt-text="Diagramm mit Datenträger Schreib Metrik über 10 Minuten" border="false":::

## <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Auswahl nicht angegeben, sodass alle ' Schreib '-Metriken ausgewählt sind. In diesem Beispiel wird davon ausgegangen, dass die-Funktion bereits installiert ist, und es wird eine Alternative direkte Aufruf Syntax verwendet, wobei die Eingabe Tabelle als erster Parameter angegeben wird:
    
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels, ysplit=axes)
```
    
:::image type="content" source="images/series-metric-fl/all-disks-write-metric-10m.png" alt-text="Diagramm mit Datenträger Schreib Metrik für alle Datenträger über 10 Minuten" border="false":::
