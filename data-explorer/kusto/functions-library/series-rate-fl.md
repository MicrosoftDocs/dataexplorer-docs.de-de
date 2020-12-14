---
title: series_rate_fl ()-Azure Daten-Explorer
description: In diesem Artikel wird die benutzerdefinierte Funktion "series_rate_fl ()" in Azure Daten-Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 12/13/2020
ms.openlocfilehash: 6678f17624225895734781f32782ed5b9bd79955
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97389143"
---
# <a name="series_rate_fl"></a>series_rate_fl ()


Die-Funktion `series_rate_fl()` berechnet die durchschnittliche Rate der metrikerhöhung pro Sekunde. Die Logik folgt der promql [Rate ()](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate) -Funktion. Es sollte für Zeitreihen von Zählers verwendet werden, die vom [Prometheus](https://prometheus.io/) -Überwachungssystem in Azure Daten-Explorer erfasst und durch [series_metric_fl ()](series-metric-fl.md)abgerufen werden.

> [!NOTE]
>`series_rate_fl()` ist eine [UDF (benutzerdefinierte Funktion)](../query/functions/user-defined-functions.md). Weitere Informationen finden Sie unter [Verwendung](#usage).

## <a name="syntax"></a>Syntax

`T | invoke series_rate_fl(`*n_bins*`)`

`T` ist eine Tabelle, die von [series_metric_fl ()](series-metric-fl.md)zurückgegeben wird. Das zugehörige Schema enthält `(timestamp:dynamic, name:string, labels:string, value:dynamic)` .

## <a name="arguments"></a>Argumente

* *n_bins*: die Anzahl der Behälter zum Angeben der Lücke zwischen den extrahierten Metrikwerten für die Berechnung der Rate. Die-Funktion berechnet den Unterschied zwischen dem aktuellen Sample und dem *n_bins* vor und dividiert ihn durch den Unterschied der jeweiligen Zeitstempel in Sekunden. Dieser Parameter ist optional und hat den Standardwert 1. Die Standardeinstellungen berechnen " [unate ()](https://prometheus.io/docs/prometheus/latest/querying/functions/#irate)", die Funktion "promql Instant trate".

## <a name="usage"></a>Verbrauch

`series_rate_fl()` ist eine benutzerdefinierte [Tabellen Funktion](../query/functions/user-defined-functions.md#tabular-function), die mit dem [Aufruf Operator](../query/invokeoperator.md)angewendet werden soll. Sie können den Code entweder in die Abfrage einbetten oder in der Datenbank installieren. Es gibt zwei Verwendungs Optionen: Ad-hoc und persistente Verwendung. Beispiele finden Sie auf den folgenden Registerkarten.

> [!NOTE]
> [series_metric_fl ()](series-metric-fl.md) wird als Teil der-Funktion verwendet und muss installiert oder eingebettet werden.

# <a name="ad-hoc"></a>[Ad-hoc](#tab/adhoc)

Fügen Sie den Code für die Ad-hoc-Verwendung mithilfe der [Let-Anweisung](../query/letstatement.md)ein. Es ist keine Berechtigung erforderlich.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_rate_fl=(tbl:(timestamp:dynamic, name:string, labels:string, value:dynamic), n_bins:int=1)
{
    tbl
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend dv = array_iff(series_greater_equals(dv, 0), dv, value)  //  handles negative difference like PromQL
    | extend rate = series_divide(dv, dt)
    | project timestamp, name, rate, labels
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Verwenden Sie für persistente Verwendung [`.create function`](../management/create-function.md) . Zum Erstellen einer Funktion ist die [Berechtigung Datenbankbenutzer](../management/access-control/role-based-authorization.md)erforderlich.

### <a name="one-time-installation"></a>Einmalige Installation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Simulate PromQL rate()")
series_rate_fl(tbl:(timestamp:dynamic, name:string, labels:string, value:dynamic), n_bins:int=1)
{
    tbl
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend dv = array_iff(series_greater_equals(dv, 0), dv, value)  //  handles negative difference like PromQL
    | extend rate = series_divide(dv, dt)
    | project timestamp, name, rate, labels
}
```

### <a name="usage"></a>Verbrauch

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-rate-fl/all-disks-write-rate-2-bins.png" alt-text="Diagramm, das die Rate der Datenträger Schreib Metrik pro Sekunde für alle Datenträger anzeigt" border="false":::

## <a name="example"></a>Beispiel

Im folgenden Beispiel wird der Haupt Datenträger zweier Hosts ausgewählt, und es wird davon ausgegangen, dass die-Funktion bereits installiert ist. In diesem Beispiel wird eine Alternative direkte Aufruf Syntax verwendet, wobei die Eingabe Tabelle als erster Parameter angegeben wird:
    
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
series_rate_fl(series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1', lookback=2h, offset=now()-datetime(2020-12-08 00:00)), n_bins=10)
| render timechart with(series=labels)
```
    
:::image type="content" source="images/series-rate-fl/main-disks-write-rate-10-bins.png" alt-text="Diagramm, das die Rate pro Sekunde der Schreib Metrik des Haupt Datenträgers in den letzten zwei Stunden mit 10 Behälter Lücke anzeigt" border="false":::
