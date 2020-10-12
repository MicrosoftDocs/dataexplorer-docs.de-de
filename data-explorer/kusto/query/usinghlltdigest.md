---
title: 'Kusto-Partition & Verfassen von zwischen Aggregations Ergebnissen: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Partitionierung und Zusammenstellung von Zwischenergebnissen von Aggregationen in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 30d4f6bd315b5a32c67570ab16b9abc3160f0177
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954483"
---
# <a name="using-hll-and-tdigest"></a>Verwenden von „hll()“ und „tdigest()“

Angenommen, Sie möchten die Anzahl der unterschiedlichen Benutzer jeden Tag in den letzten sieben Tagen berechnen. Sie können einmal täglich ausführen `summarize dcount(user)` , wobei eine Spanne in den letzten sieben Tagen gefiltert wurde. Diese Methode ist ineffizient, da bei jeder Berechnung der Berechnung sechs Tage mit der vorherigen Berechnung vorhanden sind. Sie können auch ein Aggregat für jeden Tag berechnen und diese Aggregate dann kombinieren. Diese Methode erfordert, dass Sie die letzten sechs Ergebnisse "Speichern", aber es ist viel effizienter.

Das Partitionieren von Abfragen wie beschrieben ist für einfache Aggregate, z `count()` . b. und, leicht `sum()` . Sie kann auch für komplexe Aggregate nützlich sein, z `dcount()` . b `percentiles()` . und. In diesem Thema wird erläutert, wie Kusto solche Berechnungen unterstützt.

In den folgenden Beispielen wird gezeigt, wie Sie verwenden `hll` / `tdigest` und veranschaulichen, dass die Verwendung dieser Befehle in einigen Szenarien sehr leistungsfähig ist:

> [!NOTE]
> In einigen Fällen können die dynamischen Objekte, die von `hll` oder den `tdigest` Aggregatfunktionen generiert werden, groß sein und die maxvaluesize-Standard Eigenschaft in der Codierungs Richtlinie überschreiten. Wenn dies der Fall ist, wird das Objekt als NULL erfasst.
> Wenn z. b. die Ausgabe der `hll` Funktion mit der Genauigkeits Stufe 4 persistent gespeichert wird, überschreitet die Größe des `hll` Objekts den maxvaluesize-Standardwert von 1 MB.
> Um dieses Problem zu vermeiden, ändern Sie die Codierungs Richtlinie der Spalte, wie in den folgenden Beispielen gezeigt.

```kusto
range x from 1 to 1000000 step 1
| summarize hll(x,4)
| project sizeInMb = estimate_data_size(hll_x) / pow(1024,2)
```

|sizeInMb|
|---|
|1,0000524520874|

Wenn Sie dieses Objekt in eine Tabelle einfügen, bevor Sie diese Art von Richtlinie anwenden, wird NULL erfasst:

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

```kusto
MyTable
| project isempty(hll_x)
```

| Column1 |
|---------|
| 1       |

Um das Erfassen von NULL zu vermeiden, verwenden Sie den speziellen codierungrichtlinientyp `bigobject` , der die `MaxValueSize` auf 2 MB wie folgt überschreibt:

```kusto
.alter column MyTable.hll_x policy encoding type='bigobject'
```

Ein Wert wird nun in dieselbe Tabelle oben erfasst:

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

erfasst den zweiten Wert erfolgreich: 

```kusto
MyTable
| project isempty(hll_x)
```

|Column1|
|---|
|1|
|0|


## <a name="example-count-with-binned-timestamp"></a>Beispiel: count with klassierte Zeitstempel

Es gibt eine Tabelle `PageViewsHllTDigest` mit `hll` Werten von Seiten, die in jeder Stunde angezeigt werden. Sie möchten, dass diese Werte in klassifiziert werden `12h` . Führen `hll` Sie die Werte `hll_merge()` mit der Aggregatfunktion zusammen, wobei der Zeitstempel klassifiziert ist `12h` . Verwenden Sie die-Funktion `dcount_hll` , um den endgültigen Wert zurückzugeben `dcount` :

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 12h)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|`dcount_hll_merged_hll`|
|---|---|
|2016-05-01 12:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|38797623|
|2016-05-02 12:00:00.0000000|39316056|
|2016-05-03 00:00:00.0000000|13685621|

Zum Klassifizieren von Zeitstempel `1d` :

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 1d)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|`dcount_hll_merged_hll`|
|---|---|
|2016-05-01 00:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|64135183|
|2016-05-03 00:00:00.0000000|13685621|

Die gleiche Abfrage kann über den Werten von erfolgen `tdigest` , die die `BytesDelivered` in jeder Stunde darstellen:

```kusto
PageViewsHllTDigest
| summarize merged_tdigests = merge_tdigests(tdigestBytesDel) by bin(Timestamp, 12h)
| project Timestamp , percentile_tdigest(merged_tdigests, 95, typeof(long))
```

|Timestamp|`percentile_tdigest_merged_tdigests`|
|---|---|
|2016-05-01 12:00:00.0000000|170200|
|2016-05-02 00:00:00.0000000|152975|
|2016-05-02 12:00:00.0000000|181315|
|2016-05-03 00:00:00.0000000|146817|
 
## <a name="example-temporary-table"></a>Beispiel: temporäre Tabelle

Kusto-Limits werden mit zu großen Datasets erreicht, bei denen Sie regelmäßige Abfragen für das DataSet ausführen müssen, aber die regulären Abfragen ausführen, um [`percentile()`](percentiles-aggfunction.md) große Datasets zu berechnen oder zu [`dcount()`](dcount-aggfunction.md) überschreiten.

::: zone pivot="azuredataexplorer"

Um dieses Problem zu beheben, können neu hinzugefügte Daten einer temporären Tabelle als-oder-Werte hinzugefügt werden, `hll` `tdigest` indem verwendet [`hll()`](hll-aggfunction.md) wird, wenn der erforderliche Vorgang ist, `dcount` oder [`tdigest()`](tdigest-aggfunction.md) Wenn der erforderliche Vorgang mithilfe von oder Perzentil ist [`set/append`](../../ingest-data-overview.md) [`update policy`](../management/updatepolicy.md) . In diesem Fall werden die Zwischenergebnisse von `dcount` oder `tdigest` in einem anderen Dataset gespeichert, das kleiner als das große Ziel ist.

::: zone-end

::: zone pivot="azuremonitor"

Um dieses Problem zu beheben, können neu hinzugefügte Daten einer temporären Tabelle als-oder-Werte hinzugefügt werden, `hll` `tdigest` [`hll()`](hll-aggfunction.md) Wenn der erforderliche Vorgang ist `dcount` . In diesem Fall werden die Zwischenergebnisse von `dcount` in einem anderen Dataset gespeichert, das kleiner als das große Ziel ist.

::: zone-end

Wenn Sie die endgültigen Ergebnisse dieser Werte erhalten müssen, verwenden die Abfragen möglicherweise `hll` / `tdigest` Fusionen: [`hll-merge()`](hll-merge-aggfunction.md) / [`tdigest_merge()`](tdigest-merge-aggfunction.md) . Nachdem die zusammengeführten Werte erhalten wurden, [`percentile_tdigest()`](percentile-tdigestfunction.md)  /  [`dcount_hll()`](dcount-hllfunction.md) wird möglicherweise für diese zusammengeführten Werte aufgerufen, um das Endergebnis von `dcount` oder Perzentilen zu erhalten.

Angenommen, es gibt eine Tabelle, in der die Daten täglich erfasst werden, jeden Tag, an dem Sie die unterschiedliche Anzahl von Seiten berechnen möchten, die pro Minute nach dem Date = DateTime (2016-05-01 18:00:00.0000000) angezeigt werden.

Führen Sie die folgende Abfrage aus:

```kusto
PageViews   
| where Timestamp > datetime(2016-05-01 18:00:00.0000000)
| summarize percentile(BytesDelivered, 90), dcount(Page,2) by bin(Timestamp, 1d)
```

|Timestamp|percentile_BytesDelivered_90|dcount_Page|
|---|---|---|
|2016-05-01 00:00:00.0000000|83634|20056275|
|2016-05-02 00:00:00.0000000|82770|64135183|
|2016-05-03 00:00:00.0000000|72920|13685621|

Diese Abfrage aggregiert alle Werte jedes Mal, wenn Sie diese Abfrage ausführen (z. b. Wenn Sie Sie mehrmals täglich ausführen möchten).

Wenn Sie den `hll` -Wert und den- `tdigest` Wert (die Zwischenergebnisse von `dcount` und Perzentil sind) in einer temporären Tabelle speichern, `PageViewsHllTDigest` können Sie mithilfe der Befehle Update Policy oder Set/Append die Werte nur zusammenführen und dann `dcount_hll` / `percentile_tdigest` mithilfe der folgenden Abfrage verwenden:

```kusto
PageViewsHllTDigest
| summarize  percentile_tdigest(merge_tdigests(tdigestBytesDel), 90), dcount_hll(hll_merge(hllPage)) by bin(Timestamp, 1d)
```

|Timestamp|`percentile_tdigest_merge_tdigests_tdigestBytesDel`|`dcount_hll_hll_merge_hllPage`|
|---|---|---|
|2016-05-01 00:00:00.0000000|84224|20056275|
|2016-05-02 00:00:00.0000000|83486|64135183|
|2016-05-03 00:00:00.0000000|72247|13685621|

Diese Abfrage sollte leistungsfähiger sein, da Sie über eine kleinere Tabelle ausgeführt wird. In diesem Beispiel wird die erste Abfrage über ungefähr 215 Mio. Datensätze ausgeführt, während die zweite Abfrage über nur 32 Datensätze läuft:

## <a name="example-intermediate-results"></a>Beispiel: Zwischenergebnisse

Die Beibehaltungs Abfrage.
Angenommen, Sie verfügen über eine Tabelle, die zusammengefasst wird, wenn jede Wikipedia-Seite angezeigt wird (Stichprobengröße ist 10 m), und Sie möchten für jede date1 suchen date2 den Prozentsatz der Seiten, die in date1 und date2 in Bezug auf die auf date1 (date1 < date2) angezeigten Seiten überprüft werden.
  
Der triviale Weg verwendet Join-und zusammen fassoperatoren:

```kusto
// Get the total pages viewed each day
let totalPagesPerDay = PageViewsSample
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
// Join the table to itself to get a grid where 
// each row shows foreach page1, in which two dates
// it was viewed.
// Then count the pages between each two dates to
// get how many pages were viewed between date1 and date2.
PageViewsSample
| summarize by Page, Day1 = startofday(Timestamp)
| join kind = inner
(
    PageViewsSample
    | summarize by Page, Day2 = startofday(Timestamp)
)
on Page
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

|Tag1|Tag2|Prozentwert|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|

 
Die obige Abfrage hat ungefähr 18 Sekunden gedauert.

Wenn Sie die [`hll()`](hll-aggfunction.md) Funktionen, [`hll_merge()`](hll-merge-aggfunction.md) und verwenden [`dcount_hll()`](dcount-hllfunction.md) , endet die entsprechende Abfrage nach ungefähr 1,3 Sekunden und zeigt an, dass die- `hll` Funktion die oben genannte Abfrage um ~ 14 mal beschleunigt:

```kusto
let Stats=PageViewsSample | summarize pagehll=hll(Page, 2) by day=startofday(Timestamp); // saving the hll values (intermediate results of the dcount values)
let day0=toscalar(Stats | summarize min(day)); // finding the min date over all dates.
let dayn=toscalar(Stats | summarize max(day)); // finding the max date over all dates.
let daycount=tolong((dayn-day0)/1d); // finding the range between max and min
Stats
| project idx=tolong((day-day0)/1d), day, pagehll
| mv-expand pidx=range(0, daycount) to typeof(long)
// Extend the column to get the dcount value from hll'ed values for each date (same as totalPagesPerDay from the above query)
| extend key1=iff(idx < pidx, idx, pidx), key2=iff(idx < pidx, pidx, idx), pages=dcount_hll(pagehll)
// For each two dates, merge the hll'ed values to get the total dcount over each two dates, 
// This helps to get the pages viewed in both date1 and date2 (see the description below about the intersection_size)
| summarize (day1, pages1)=arg_min(day, pages), (day2, pages2)=arg_max(day, pages), union_size=dcount_hll(hll_merge(pagehll)) by key1, key2
| where day2 > day1
// To get pages viewed in date1 and also date2, look at the merged dcount of date1 and date2, subtract it from pages of date1 + pages on date2.
| project pages1, day1,day2, intersection_size=(pages1 + pages2 - union_size)
| project day1, day2, Percentage = intersection_size*100.0 / pages1
```

|"Day1|"day2|Prozentwert|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|33.2298494510578|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.9773830213667|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.5160020350006|

> [!NOTE] 
> Die Ergebnisse der Abfragen sind aufgrund des Fehlers der Funktionen nicht 100% genau `hll` . Weitere Informationen zu den Fehlern finden Sie unter [`dcount()`](dcount-aggfunction.md) .
