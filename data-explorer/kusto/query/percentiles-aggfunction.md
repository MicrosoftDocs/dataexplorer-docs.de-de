---
title: Percentile (), percentiles ()-Azure Daten-Explorer
description: In diesem Artikel werden Percentile (), perzentiles () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 13cc0edad5e0e4673c34e7e5b1b517f097fa4e9a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346183"
---
# <a name="percentile-percentiles-aggregation-function"></a>Percentile (), percentiles () (Aggregations Funktion)

Gibt eine Schätzung für das angegebene [Perzentil](#nearest-rank-percentile) mit dem nächstliegenden Rang der durch definierten Population zurück `*Expr*` .
Die Genauigkeit hängt von der Bevölkerungsdichte in der Region des Perzentils ab. Diese Funktion kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) "Zusammenfassung" verwendet werden.

* `percentiles()`ist wie `percentile()` , berechnet jedoch eine Reihe von perzentilwerten, die schneller als jedes Quantil berechnet werden.
* `percentilesw()`ähnelt `percentilew()` , berechnet jedoch eine Reihe gewichteter Perzentil-Werte, die schneller als jedes Quantil berechnet werden.
* `percentilew()`und `percentilesw()` können gewichtete Perzentilen berechnen. Gewichtete Perzentile berechnen die angegebenen Perzentile in einer gewichteten Weise, indem Sie jeden Wert `weight` in der Eingabe als Wiederholungszeit behandeln.

## <a name="syntax"></a>Syntax

`percentile(` *expr* - `,` *Perzentil* zusammenfassen`)`

`percentiles(` *expr* zusammenfassen `,` *Percentile1* [ `,` *Percentile2*]`)`

`percentiles_array(` *expr* zusammenfassen `,` *Percentile1* [ `,` *Percentile2*]`)`

`percentiles_array(` *expr* `,` *Dynamic Array* zusammenfassen`)`

`percentilew(` *expr* - `,` gewil-Perzentil *WeightExpr* `,` *Percentile* zusammenfassen`)`

`percentilesw(` *expr* -Gewichtung zusammenfassen `,` *WeightExpr* `,` *Percentile1* [ `,` *Percentile2*]`)`

`percentilesw_array(` *expr* -Gewichtung zusammenfassen `,` *WeightExpr* `,` *Percentile1* [ `,` *Percentile2*]`)`

`percentilesw_array(`dynamisches Array von *expr* `,` *weightexpr* `,` *Dynamic array* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* `*Expr*`: Ausdruck, der für die Aggregations Berechnung verwendet wird.
* `*WeightExpr*`: Ausdruck, der als Gewichtung von Werten für die Aggregations Berechnung verwendet wird.
* `*Percentile*`: Eine Double-Konstante, die das Quantil angibt.
* `*Dynamic array*`: Liste der Perzentile in einem dynamischen Array von ganzzahligen oder Gleit Komma Zahlen.

## <a name="returns"></a>Rückgabe

Gibt eine Schätzung für `*Expr*` die angegebenen Perzentilen in der Gruppe zurück. 

## <a name="examples"></a>Beispiele

Der Wert von `Duration` ist größer als 95% der Stichproben Menge und kleiner als 5% der Stichproben Menge.

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

Gleichzeitige Berechnung von 5, 50 (Median) und 95.

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/percentiles-aggfunction/percentiles.png" alt-text="Perzentile":::

Die Ergebnisse zeigen, dass in Europa 5% der Anrufe kürzer als 11.55 s sind, 50% der Anrufe kürzer als 3 Minuten, 18,46 Sekunden und 95% der Anrufe kürzer als 40 Minuten 48 Sekunden.

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>Gewichtete Perzentile

Angenommen, Sie messen die Zeit (Dauer), die eine Aktion durchgeführt wird, wiederholt. Anstatt jeden Wert der Messung aufzuzeichnen, notieren Sie jeden Wert der Dauer, gerundet auf 100 MS und die Häufigkeit, mit der der gerundete Wert aufgetreten ist (BucketSize).

Verwenden `summarize percentilesw(Duration, BucketSize, ...)` Sie, um die angegebenen Perzentilen auf eine gewichtete Weise zu berechnen. Behandeln Sie jeden Wert der Dauer so, als ob er die BucketSize-Zeiten in der Eingabe wiederholt hätte, ohne diese Datensätze tatsächlich materialisieren zu müssen.

## <a name="example"></a>Beispiel

Ein Kunde verfügt über eine Reihe von Latenz Werten in Millisekunden: `{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }` .

Um die Bandbreite und den Speicherplatz zu reduzieren, führen Sie eine vorab Aggregation der folgenden Bucket durch: `{ 10, 20, 30, 40, 50, 100 }` . Zählen Sie die Anzahl der Ereignisse in jedem Bucket, um die folgende Tabelle zu bilden:

:::image type="content" source="images/percentiles-aggfunction/percentilesw-table.png" alt-text="Percentilesw-Tabelle":::

In der Tabelle wird Folgendes angezeigt:
 * Acht Ereignisse im Bucket "10 MS" (entsprechend der Teilmenge `{ 1, 1, 2, 2, 2, 5, 7, 7 }` )
 * Sechs Ereignisse im Bucket mit 20 ms (entsprechend der Teilmenge `{ 12, 12, 15, 15, 15, 18 }` )
 * Drei Ereignisse im Bucket "30 ms" (entsprechend der Teilmenge `{ 21, 22, 26 }` )
 * Ein Ereignis im Bucket 40-ms (entsprechend der Teilmenge `{ 35 }` )

An diesem Punkt sind die ursprünglichen Daten nicht mehr verfügbar. Nur die Anzahl der Ereignisse in jedem Bucket. Verwenden Sie die-Funktion, um Perzentilen aus diesen Daten zu berechnen `percentilesw()` .
Verwenden Sie z. b. die folgende Abfrage für die Quantile 50, 75 und 99,9.

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

:::image type="content" source="images/percentiles-aggfunction/percentilesw-result.png" alt-text="Perzentilesw-Ergebnis" border="false":::


Die obige Abfrage entspricht der-Funktion `percentiles(LatencyBucket, 50, 75, 99.9)` , wenn die Daten in die folgende Form erweitert wurden:

:::image type="content" source="images/percentiles-aggfunction/percentilesw-rawtable.png" alt-text="Percentilesw-Rohtabelle":::

## <a name="getting-multiple-percentiles-in-an-array"></a>Erhalten von mehreren Perzentilen in einem Array

Mehrere Perzentilen können als Array in einer einzelnen dynamischen Spalte und nicht in mehreren Spalten abgerufen werden.

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/percentiles-aggfunction/percentiles-array-result.png" alt-text="Perzentiles Array Ergebnis":::

Entsprechend können gewichtete Perzentile mithilfe von als dynamisches Array zurückgegeben werden `percentilesw_array` .

Perzentile für `percentiles_array` und `percentilesw_array` können in einem dynamischen Array von ganzzahligen oder Gleit Komma Zahlen angegeben werden. Das Array muss konstant sein, muss jedoch kein Literalwert sein.

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>Perzentil für den nächstgelegenen Rang

Das *P*-Th-Perzentil (0 < *p* <= 100) einer Liste von sortierten Werten, die von der geringsten zum größten Wert sortiert ist, ist der kleinste Wert in der Liste. Der *p* -Prozentsatz der Daten ist kleiner oder gleich dem Wert des *p*-ten Perzentils ([aus Wikipedia-Artikel zu Perzentilen](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method)).

Definieren Sie *0*-te Perzentile als kleinsten Member der Population.

>[!NOTE]
> Aufgrund der Näherungs Art der Berechnung ist der tatsächliche zurückgegebene Wert möglicherweise kein Member der Population.
> Definition mit dem nächstgelegenen Rang bedeutet, dass *P*= 50 nicht der [interpolativen Definition des Median](https://en.wikipedia.org/wiki/Median)entspricht. Beim Auswerten der Bedeutung dieser Abweichung für die jeweilige Anwendung sollten die Größe der Auffüllung und ein [Schätz Fehler](#estimation-error-in-percentiles) berücksichtigt werden.

## <a name="estimation-error-in-percentiles"></a>Schätzungsfehler in Perzentilen

Das Perzentilaggregat stellt einen ungefähren Wert mithilfe von [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)bereit.

>[!NOTE]
> * Die Grenzen für den Schätzungsfehler variieren je nach dem Wert des angeforderten Perzentils. Die beste Genauigkeit finden Sie an beiden Enden der [0.. 100]-Skala. Percentile 0 und 100 sind die genauen minimalen und maximalen Werte der Verteilung. Die Genauigkeit nimmt zur Mitte der Skala hin ab. Es ist am Mittelwert am Mittelwert und ist auf 1% begrenzt.
> * Fehlergrenzen werden in Bezug auf den Rang, nicht auf den Wert sichtbar. Nehmen Sie an, dass Perzentil (X, 50) den Wert XM zurückgegeben hat. Die Schätzung garantiert, dass mindestens 49% und höchstens 51% der Werte von X kleiner oder gleich XM sind. Es gibt keine theoretische Beschränkung hinsichtlich des Unterschieds zwischen XM und dem tatsächlichen Medianwert von X.
> * Die Schätzung kann manchmal zu einem präzisen Wert führen, aber es gibt keine zuverlässigen Bedingungen, um zu definieren, wann dies der Fall ist.
