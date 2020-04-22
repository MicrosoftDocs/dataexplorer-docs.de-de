---
title: Partitionieren und Erstellen von Zwischenergebnissen von Aggregationen - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt das Partitionieren und Erstellen von Zwischenergebnissen von Aggregationen in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1392105e4b5d99f2273b686ed74a161750ee6f8c
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81662963"
---
# <a name="partitioning-and-composing-intermediate-results-of-aggregations"></a>Partitionieren und Komponieren von Zwischenergebnissen von Aggregationen

Angenommen, Sie möchten die Anzahl der verschiedenen Benutzer in den letzten sieben Tagen und jeden Tag berechnen. Eine Möglichkeit wäre, einmal `summarize dcount(user)` täglich mit einer auf die letzten sieben Tage gefilterten Spanne zu laufen. Dies ist ineffizient, da jedes Mal, wenn die Berechnung ausgeführt wird, eine Sechs-Tage-Überlappung mit der vorherigen Berechnung besteht. Eine weitere Möglichkeit besteht darin, ein Aggregat für jeden Tag zu berechnen und diese Aggregate dann effizient zu kombinieren. Diese Option erfordert, dass Sie sich die letzten sechs Ergebnisse "erinnern", aber es ist viel effizienter.

Das Partitionieren solcher Abfragen wäre für einfache Aggregate wie count() und sum() einfach. Es ist jedoch möglich, sie auch für komplexere Aggregate wie dcount() und perzentile() auszuführen. In diesem Thema wird erläutert, wie Kusto solche Berechnungen unterstützt.

Die folgenden Beispiele zeigen, wie hll/tdigest verwendet wird, und zeigen, dass die Verwendung dieser Methoden in einigen Szenarien möglicherweise viel leistungsstärker ist als auf andere Weise:

> [!NOTE]
> In einigen Fällen können die `hll` dynamischen `tdigest` Objekte, die von den oder den Aggregatfunktionen generiert werden, groß sein und die standardmäßige MaxValueSize-Eigenschaft in der Codierungsrichtlinie überschreiten. Wenn dies der Zuspruch bereits der Falle ist, wird das Objekt als NULL aufgenommen.
Wenn Sie z. B. `hll` die Ausgabe der Funktion mit Genauigkeitsstufe 4 beibehalten, überschreitet die Größe des hll-Objekts die standardmäßige MaxValueSize-Wertgröße von 1 MB.

```kusto
range x from 1 to 1000000 step 1
| summarize hll(x,4)
| project sizeInMb = estimate_data_size(hll_x) / pow(1024,2)
```

|sizeInMb|
|---|
|1.0000524520874|

Wenn Sie dies in eine Tabelle aufnehmen, bevor Sie diese Art von Richtlinie anwenden, wird NULL aufgenommen:

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

Um dies zu vermeiden, verwenden `bigobject`Sie den speziellen Codierungsrichtlinientyp , der die MaxValueSize wie folgt auf 2 MB überschreibt:

```kusto
.alter column MyTable.hll_x policy encoding type='bigobject'
```



Also einen Wert jetzt in dieselbe Tabelle oben ein:

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```
nimmt den zweiten Wert erfolgreich auf: 

```kusto
MyTable
| project isempty(hll_x)
```

|Column1|
|---|
|1|
|0|


**Beispiel**

Angenommen, es gibt eine Tabelle, PageViewsHllTDigest, die die hll-Werte über Seiten in jeder Stunde angezeigt hat. Das Ziel besteht darin, diese Werte `12h`abzubekommen, aber an zu binden, um die hll-Werte mithilfe der Aggregatfunktion hll_merge() nach Zeitstempel zu verschmelzen, die auf `12h`. Rufen Sie dann die Funktion auf, dcount_hll, um den endgültigen dcount-Wert abzurufen:

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 12h)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|dcount_hll_merged_hll|
|---|---|
|2016-05-01 12:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|38797623|
|2016-05-02 12:00:00.0000000|39316056|
|2016-05-03 00:00:00.0000000|13685621|

Oder sogar für binned `1d`Zeitstempel für:

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 1d)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|dcount_hll_merged_hll|
|---|---|
|2016-05-01 00:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|64135183|
|2016-05-03 00:00:00.0000000|13685621|

 Dasselbe kann über die Werte von tdigest geschehen, die die BytesDelivered in jeder Stunde darstellen:

```kusto
PageViewsHllTDigest
| summarize merged_tdigests = merge_tdigests(tdigestBytesDel) by bin(Timestamp, 12h)
| project Timestamp , percentile_tdigest(merged_tdigests, 95, typeof(long))
```

|Timestamp|percentile_tdigest_merged_tdigests|
|---|---|
|2016-05-01 12:00:00.0000000|170200|
|2016-05-02 00:00:00.0000000|152975|
|2016-05-02 12:00:00.0000000|181315|
|2016-05-03 00:00:00.0000000|146817|
 
**Beispiel**

Kusto-Grenzwerte werden mit zu großen Datasets erreicht, bei denen Sie periodische Abfragen über [`percentile()`](percentiles-aggfunction.md) [`dcount()`](dcount-aggfunction.md) das Dataset ausführen müssen, aber die regulären Abfragen ausführen müssen, um diese großen Datasets zu berechnen oder zu übersteigen.

::: zone pivot="azuredataexplorer"

Um dieses Problem zu lösen, können neu hinzugefügte Daten zu einer [`hll()`](hll-aggfunction.md) temporären Tabelle als [`tdigest()`](tdigest-aggfunction.md) hll- oder tdigest-Werte hinzugefügt werden, wenn der erforderliche Vorgang dcount ist oder wenn der erforderliche Vorgang perzentil ist, oder [`set/append`](../management/data-ingestion/index.md) , [`update policy`](../management/updatepolicy.md)In diesem Fall werden die Zwischenergebnisse von dcount oder tdigest in einem anderen Dataset gespeichert, das kleiner als der große Zielvorgang sein sollte.

::: zone-end

::: zone pivot="azuremonitor"

Um dieses Problem zu lösen, können neu hinzugefügte Daten zu einer [`hll()`](hll-aggfunction.md) temporären Tabelle als hll- oder tdigest-Werte hinzugefügt werden, wenn der erforderliche Vorgang dcount ist. In diesem Fall werden die Zwischenergebnisse von dcount in einem anderen Dataset gespeichert, das kleiner als das große Ziel sein sollte.

::: zone-end

Wenn Sie die endgültigen Ergebnisse dieser Werte abrufen müssen, können die Abfragen [`hll-merge()`](hll-merge-aggfunction.md) / [`tdigest_merge()`](tdigest-merge-aggfunction.md)hll/tdigest-Zusammenschlüsse [`percentile_tdigest()`](percentile-tdigestfunction.md)  /  [`dcount_hll()`](dcount-hllfunction.md) verwenden: , Dann, nach dem Abrufen der zusammengeführten Werte, können diese zusammengeführten Werte aufgerufen werden, um das Endergebnis von dcount oder Perzentilen zu erhalten.

Angenommen, es gibt eine Tabelle, PageViews, in die Daten täglich aufgenommen werden, jeden Tag, an dem Sie die eindeutige Anzahl der Seiten berechnen möchten, die pro Minuite später als Date = datetime(2016-05-01 18:00:00.0000000) angezeigt werden.

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

Diese Abfrage aggregiert alle Werte jedes Mal, wenn Sie diese Abfrage ausführen (z. B. wenn Sie sie mehrmals täglich ausführen möchten).

Wenn Sie die hll- und tdigest-Werte (die zwischengeschalteten Ergebnisse von dcount und perpertil) in einer temporären Tabelle, PageViewsHllTDigest, mithilfe einer Aktualisierungsrichtlinie oder set/append-Befehle speichern, können Sie die Werte nur zusammenführen und dann dcount_hll/percentile_tdigest mit der folgenden Abfrage verwenden:

```kusto
PageViewsHllTDigest
| summarize  percentile_tdigest(merge_tdigests(tdigestBytesDel), 90), dcount_hll(hll_merge(hllPage)) by bin(Timestamp, 1d)
```

|Timestamp|percentile_tdigest_merge_tdigests_tdigestBytesDel|dcount_hll_hll_merge_hllPage|
|---|---|---|
|2016-05-01 00:00:00.0000000|84224|20056275|
|2016-05-02 00:00:00.0000000|83486|64135183|
|2016-05-03 00:00:00.0000000|72247|13685621|

Dies sollte leistungsstärker sein, und die Abfrage wird über eine kleinere Tabelle ausgeführt (in diesem Beispiel wird die erste Abfrage über 215M-Datensätze ausgeführt, während die zweite nur über 32 Datensätze ausgeführt wird).

**Beispiel**

Die Aufbewahrungsabfrage.
Angenommen, Sie haben eine Tabelle, die zusammenfasst, wann jede Wikipedia-Seite aufgerufen wurde (Beispielgröße ist 10M), und Sie möchten für jedes Datum1 Datum2 den Prozentsatz der Seiten suchen, die sowohl in Datum1 als auch im Datum2 relativ zu den am Datum 1 angezeigten Seiten (Datum1 < Datum2) überprüft wurden.
  
Die triviale Verwendung von Verknüpfungs- und Zusammenfassungsoperatoren:

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

 
Die obige Abfrage dauerte 18 Sekunden.

Bei Verwendung der [`hll()`](hll-aggfunction.md) [`hll_merge()`](hll-merge-aggfunction.md) Funktionen [`dcount_hll()`](dcount-hllfunction.md)von und wird die entsprechende Abfrage nach 1,3 Sekunden beendet und zeigt an, dass hll-Funktionen die obige Abfrage um das 14-fache beschleunigen:

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

|tag1|tag2|Prozentwert|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|33.2298494510578|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.9773830213667|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.5160020350006|

> [!NOTE] 
> Die Ergebnisse der Abfragen sind aufgrund des Fehlers der hll-Funktionen nicht 100% genau. (siehe [`dcount()`](dcount-aggfunction.md) weitere Informationen zu den Fehlern).
