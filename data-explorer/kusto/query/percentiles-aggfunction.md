---
title: Perzentil(), Perzentile() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Perzentil(), Perzentile() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: ecbb56305cfc43033ca172071f48b25768de6d2f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663660"
---
# <a name="percentile-percentiles"></a>Perzentil(), Perzentile()

Gibt eine Schätzung für das angegebene [Perzentil](#nearest-rank-percentile) des nächsten Rangs der von *Expr*definierten Grundgesamtheit zurück. Die Genauigkeit hängt von der Bevölkerungsdichte in der Region des Perzentils ab. Diese Funktion kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden.

* `percentiles()`ist `percentile()`wie , berechnet jedoch eine Anzahl von Perzentilwerten (die schneller ist als die individuelle Berechnung jedes Perzentils).
* `percentilesw()`ist `percentilew()`wie , berechnet jedoch eine Anzahl gewichteter Perzentilwerte (die schneller ist als die individuelle Berechnung jedes Perzentils).
* `percentilew()`und `percentilesw()` ermöglicht die Berechnung gewichteter Perzentile. Gewichtete Perzentile berechnen die angegebenen Perzentile auf "gewichtete" Weise - `Weight` wobei jeder Wert so behandelt wird, als ob er in der Eingabe wiederholt wurde.

**Syntax**

`percentile(` *Expr* `,` *Perzentil* zusammenfassen`)`

`percentiles(` *Expr* `,` *Perzentil1* [`,` *Perzentil2*] zusammenfassen`)`

`percentiles_array(` *Expr* `,` *Perzentil1* [`,` *Perzentil2*] zusammenfassen`)`

`percentiles_array(` *Expr* `,` *Dynamic-Array* zusammenfassen`)`

`percentilew(` *Expr* `,` *WeightExpr* `,` *Perzentil* zusammenfassen`)`

`percentilesw(` *Expr* `,` *WeightExpr* `,` *Perzentil1* [`,` *Perzentil2*] zusammenfassen`)`

`percentilesw_array(` *Expr* `,` *WeightExpr* `,` *Perzentil1* [`,` *Perzentil2*] zusammenfassen`)`

`percentilesw_array(` *Expr* `,` *WeightExpr* `,` *Dynamic-Array* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird.
* *WeightExpr*: Ausdruck, der als Gewicht der Werte für die Aggregationsberechnung verwendet wird.
* *Perzentil* ist eine doppelte Konstante, die das Perzentil angibt.
* *Dynamisches Array*: Liste der Perzentile in einem dynamischen Array von Ganzzahl- oder Gleitkommazahlen.

**Rückgabe**

Gibt eine Schätzung für *Expr* der angegebenen Perzentile in der Gruppe zurück. 

**Beispiele**

Der Wert `Duration` dieser Werte ist größer als 95 % des Stichprobensatzes und kleiner als 5 % des Stichprobensatzes:

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

Berechnen Sie gleichzeitig 5, 50 (Median) und 95:

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/aggregations/percentiles.png" alt-text="Perzentile":::

Die Ergebnisse zeigen, dass in Europa 5 % der Anrufe kürzer als 11,55 s sind, 50 % der Anrufe kürzer als 3 Minuten, 18,46 Sekunden und 95 % der Anrufe kürzer als 40 Minuten und 48 Sekunden sind.

Berechnen Sie mehrere Statistiken:

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>Gewichtete Perzentile

Angenommen, Sie messen die Zeit (Dauer), die eine Aktion immer wieder abgeschlossen werden muss. Anstatt jeden Wert der Messung aufzuzeichnen, verdichten Sie sie, indem Sie jeden Wert von Duration aufzeichnen, gerundet auf 100 msec und wie oft dieser gerundete Wert angezeigt wird (BucketSize).

Sie können `summarize percentilesw(Duration, BucketSize, ...)` die angegebenen Perzentile auf "gewichtete" Weise berechnen und jeden Wert von Duration so behandeln, als ob es BucketSize-Zeiten in der Eingabe wiederholt hätte, ohne diese Datensätze tatsächlich materialisieren zu müssen.

Beispiel: Ein Kunde verfügt über einen Satz von `{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }`Latenzwerten in Millisekunden: .

Um Bandbreite und Speicher zu reduzieren, führen Sie `{ 10, 20, 30, 40, 50, 100 }`die Voraggregation auf die folgenden Buckets aus: aus, und zählen Sie die Anzahl der Ereignisse in jedem Bucket, die die folgende Kusto-Tabelle enthält:

:::image type="content" source="images/aggregations/percentilesw-table.png" alt-text="Percentilesw-Tabelle":::

Was als zu lesen ist wie:
 * 8 Ereignisse im 10ms-Bucket (entsprechend Teilmenge `{ 1, 1, 2, 2, 2, 5, 7, 7 }`)
 * 6 Ereignisse im 20ms-Bucket (entsprechend Teilmenge `{ 12, 12, 15, 15, 15, 18 }`)
 * 3 Ereignisse im 30ms-Bucket (entsprechend Teilmenge `{ 21, 22, 26 }`)
 * 1 Ereignis im 40ms-Bucket (entsprechend Teilmenge `{ 35 }`)

Zu diesem Zeitpunkt sind die ursprünglichen Daten nicht mehr verfügbar, und alles, was wir haben, ist die Anzahl der Ereignisse in jedem Bucket. Um Perzentile aus diesen Daten `percentilesw()` zu berechnen, verwenden Sie die Funktion. Verwenden Sie beispielsweise für die Perzentile 50, 75 und 99,9 die folgende Abfrage: 

```kusto
datatable (ReqCount:long, LatencyBucket:long) 
[ 
    8, 10, 
    6, 20, 
    3, 30, 
    1, 40 
]
| summarize percentilesw(LatencyBucket, ReqCount, 50, 75, 99.9) 
```

Das Ergebnis der obigen Abfrage lautet:

:::image type="content" source="images/aggregations/percentilesw-result.PNG" alt-text="Percentilesw-Ergebnis":::

Beachten Sie, dass die `percentiles(LatencyBucket, 50, 75, 99.9)` obige Abfrage der Funktion entspricht, wenn die Daten in der folgenden Form ausgegeben wurden:

:::image type="content" source="images/aggregations/percentilesw-rawtable.png" alt-text="Perzentilsw-Rohtabelle":::

## <a name="getting-multiple-percentiles-in-an-array"></a>Abrufen mehrerer Perzentile in einem Array

Mehrere Perzentile können als Array in einer einzelnen dynamischen Spalte anstelle mehrerer Spalten abgerufen werden: 

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/aggregations/percentiles-array-result.png" alt-text="Percentiles-Array-Ergebnis":::

Ebenso können gewichtete Perzentile als dynamisches Array zurückgegeben werden,`percentilesw_array`

Perzentile `percentiles_array` für `percentilesw_array` und können in einem dynamischen Array von Ganzzahl- oder Gleitkommazahlen angegeben werden. Das Array muss konstant sein, muss aber nicht literal sein.

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>Nächstes Rang Perzentil
*P*-th Perzentil (0 < *P* <= 100) einer Liste von geordneten Werten (sortiert von am wenigsten nach dem größten) ist der kleinste Wert in der Liste, so dass *P* Prozent der Daten kleiner oder gleich diesem Wert ist[(aus Wikipedia-Artikel über Perzentile](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method))

Definieren Sie *0*-th Perzentile als kleinstes Mitglied der Grundgesamtheit.

>[!NOTE]
> Angesichts der Annäherung der Berechnung kann der tatsächlich zurückgegebene Wert kein Mitglied der Bevölkerung sein.
> Die Definition des nächsten Rangs bedeutet, dass *P*=50 nicht der [interpolativen Definition des Medians](https://en.wikipedia.org/wiki/Median)entspricht. Bei der Bewertung der Bedeutung dieser Diskrepanz für die jeweilige Anwendung sollten die Größe der Grundgesamtheit und ein [Schätzfehler](#estimation-error-in-percentiles) berücksichtigt werden.

## <a name="estimation-error-in-percentiles"></a>Schätzungsfehler in Perzentilen

Das Perzentilaggregat stellt einen ungefähren Wert mithilfe von [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)bereit. 

>[!NOTE]
> * Die Grenzen für den Schätzungsfehler variieren je nach dem Wert des angeforderten Perzentils. Die beste Genauigkeit liegt an den Enden der [0..100] Skala. Die Prozente 0 und 100 sind die genauen Minimal- und Maximalwerte der Verteilung. Die Genauigkeit nimmt zur Mitte der Skala hin ab. Es ist am schlechtesten im Median und ist auf 1% begrenzt. 
> * Fehlergrenzen werden in Bezug auf den Rang, nicht auf den Wert sichtbar. Angenommen, Perzentil (X, 50) hat den Wert Xm zurückgegeben. Die Schätzung garantiert, dass mindestens 49 % und höchstens 51 % der Werte von X kleiner oder gleich Xm sind. Es gibt keine theoretische Grenze für die Differenz zwischen Xm und dem tatsächlichen Medianwert von X.
> * Die Schätzung kann manchmal zu einem genauen Wert führen, aber es gibt keine verlässlichen Bedingungen, um zu definieren, wann dies der Fall sein wird.
