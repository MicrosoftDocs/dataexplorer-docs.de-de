---
title: Perzentil (), perzentiles ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden Percentile (), perzentiles () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 58d6458f0a5cf514b1acd240c9adede2022f028b
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82619098"
---
# <a name="percentile-percentiles"></a>Perzentil (), Quantile ()

Gibt eine Schätzung für das angegebene [Perzentil](#nearest-rank-percentile) mit dem nächstliegenden Rang der durch *expr*definierten Population zurück. Die Genauigkeit hängt von der Bevölkerungsdichte in der Region des Perzentils ab. Diese Funktion kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) "Zusammenfassung" verwendet werden.

* `percentiles()`ist wie `percentile()`, berechnet jedoch eine Reihe von perzentilwerten (was schneller ist, als jedes Quantil einzeln zu berechnen).
* `percentilesw()`ist wie `percentilew()`, berechnet jedoch eine Reihe gewichteter Perzentilwerte (was schneller ist, als jedes Quantil einzeln zu berechnen).
* `percentilew()`und `percentilesw()` ermöglichen das berechnen gewichteter Perzentilen. Gewichtete Perzentile berechnen die angegebenen Perzentile in einer "gewichteten" Art und Weise, wie die einzelnen Werte `Weight` so behandelt werden, als ob Sie in der Eingabe wiederholt wurden.

**Syntax**

`percentile(` *expr* `,` - *Perzentil* zusammenfassen`)`

`percentiles(` *expr* `,` zusammenfassen *Percentile1* [`,` *Percentile2*]`)`

`percentiles_array(` *expr* `,` zusammenfassen *Percentile1* [`,` *Percentile2*]`)`

`percentiles_array(` *expr* `,` *Dynamic Array* zusammenfassen`)`

`percentilew(` *expr* `,` - *gewil-Perzentil* `,` *Percentile* zusammenfassen`)`

`percentilesw(` *expr* `,` - *Gewichtung* `,` zusammenfassen *Percentile1* [`,` *Percentile2*]`)`

`percentilesw_array(` *expr* `,` - *Gewichtung* `,` zusammenfassen *Percentile1* [`,` *Percentile2*]`)`

`percentilesw_array(` *dynamisches Array* von *expr* `,` *weightexpr* `,` zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird.
* *Weightexpr*: Ausdruck, der als Gewichtung von Werten für die Aggregations Berechnung verwendet wird.
* *Percentile* ist eine Double-Konstante, die das Quantil angibt.
* *Dynamisches Array*: Liste der Perzentile in einem dynamischen Array von ganzzahligen oder Gleit Komma Zahlen.

**Rückgabe**

Gibt eine Schätzung für *expr* der angegebenen Quantilen in der Gruppe zurück. 

**Beispiele**

Der Wert von `Duration` ist größer als 95% der Stichproben Menge und kleiner als 5% der Stichproben Menge:

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

Gleichzeitige Berechnung von 5, 50 (Median) und 95:

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/percentiles-aggfunction/percentiles.png" alt-text="Perzentile":::

Die Ergebnisse zeigen, dass in Europa 5% der Anrufe kürzer als 11.55 s sind, 50% der Anrufe kürzer als 3 Minuten, 18,46 Sekunden und 95% der Anrufe kürzer als 40 Minuten 48 Sekunden.

Berechnen Sie mehrere Statistiken:

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>Gewichtete Perzentile

Angenommen, Sie messen die Zeit (Dauer), in der eine Aktion wiederholt ausgeführt wird. Anstatt jeden Wert der Messung aufzuzeichnen, werden Sie durch Aufzeichnen der einzelnen Werte der Dauer, gerundet auf 100 MB und der Häufigkeit, mit der der gerundete Wert auftrat (BucketSize).

Sie können verwenden `summarize percentilesw(Duration, BucketSize, ...)` , um die angegebenen Perzentile in einem "gewichteten" Verfahren zu berechnen, der jeden Wert der Dauer so behandelt, als ob es sich um wiederholte BucketSize-Zeiten in der Eingabe handelt, ohne diese Datensätze tatsächlich materialisieren zu müssen.

Beispiel: ein Kunde verfügt über eine Reihe von Latenz Werten in Millisekunden `{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }`:.

Um die Bandbreite und den Speicherplatz zu reduzieren, führen Sie vorab Aggregationen für die folgenden Bucket aus: `{ 10, 20, 30, 40, 50, 100 }`und zählen die Anzahl der Ereignisse in jedem Bucket, der die folgende Kusto-Tabelle liefert:

:::image type="content" source="images/percentiles-aggfunction/percentilesw-table.png" alt-text="Percentilesw-Tabelle":::

Diese können wie folgt gelesen werden:
 * 8 Ereignisse im Bucket 10 ms (entsprechend der Teilmenge `{ 1, 1, 2, 2, 2, 5, 7, 7 }`)
 * 6 Ereignisse im Bucket 20 ms (entsprechend der Teilmenge `{ 12, 12, 15, 15, 15, 18 }`)
 * 3 Ereignisse im Bucket 30ms (entsprechend der Teilmenge `{ 21, 22, 26 }`)
 * 1 Ereignis im Bucket 40 ms (entsprechend der Teilmenge `{ 35 }`)

An diesem Punkt sind die ursprünglichen Daten nicht mehr verfügbar, und es gibt nur die Anzahl der Ereignisse in jedem Bucket. Verwenden Sie die `percentilesw()` -Funktion, um Perzentilen aus diesen Daten zu berechnen. Verwenden Sie z. b. für die Quantilen 50, 75 und 99,9 die folgende Abfrage: 

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


Beachten Sie, dass die obige Abfrage der- `percentiles(LatencyBucket, 50, 75, 99.9)` Funktion entspricht, wenn die Daten in der folgenden Form verbraucht wurden:

:::image type="content" source="images/percentiles-aggfunction/percentilesw-rawtable.png" alt-text="Percentilesw-Rohtabelle":::

## <a name="getting-multiple-percentiles-in-an-array"></a>Erhalten von mehreren Perzentilen in einem Array

Mehrere Perzentilen können als Array in einer einzelnen dynamischen Spalte anstelle mehrerer Spalten abgerufen werden: 

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/percentiles-aggfunction/percentiles-array-result.png" alt-text="Perzentiles Array Ergebnis":::

Entsprechend können gewichtete Perzentile als dynamisches Array zurückgegeben werden. verwenden Sie dazu`percentilesw_array`

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
Das *p*-Th-Perzentil (0 < *P* <= 100) einer Liste von sortierten Werten (von der geringsten zum größten sortiert) ist der kleinste Wert in der Liste, sodass *P* Prozent der Daten kleiner oder gleich diesem Wert sind ([aus Wikipedia-Artikel zu Perzentilen](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method)).

Definieren Sie *0*-te Perzentile als kleinsten Member der Population.

>[!NOTE]
> Aufgrund der Näherungs Art der Berechnung ist der tatsächliche zurückgegebene Wert möglicherweise kein Member der Population.
> Definition mit dem nächstgelegenen Rang bedeutet, dass *P*= 50 nicht der [interpolativen Definition des Median](https://en.wikipedia.org/wiki/Median)entspricht. Beim Auswerten der Bedeutung dieser Abweichung für die jeweilige Anwendung sollten die Größe der Auffüllung und ein [Schätz Fehler](#estimation-error-in-percentiles) berücksichtigt werden.

## <a name="estimation-error-in-percentiles"></a>Schätzungsfehler in Perzentilen

Das Perzentilaggregat stellt einen ungefähren Wert mithilfe von [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)bereit. 

>[!NOTE]
> * Die Grenzen für den Schätzungsfehler variieren je nach dem Wert des angeforderten Perzentils. Die beste Genauigkeit finden Sie an den Enden der [0.. 100]-Skala. Percentile 0 und 100 sind die genauen minimalen und maximalen Werte der Verteilung. Die Genauigkeit nimmt zur Mitte der Skala hin ab. Es ist am Mittelwert am Mittelwert und ist auf 1% begrenzt. 
> * Fehlergrenzen werden in Bezug auf den Rang, nicht auf den Wert sichtbar. Nehmen Sie an, dass Perzentil (X, 50) den Wert XM zurückgegeben hat. Die Schätzung garantiert, dass mindestens 49% und höchstens 51% der Werte von X kleiner oder gleich XM sind. Es gibt keine theoretische Beschränkung hinsichtlich des Unterschieds zwischen XM und dem tatsächlichen Medianwert von X.
> * Die Schätzung kann manchmal zu einem präzisen Wert führen, aber es gibt keine zuverlässigen Bedingungen, um zu definieren, wann dies der Fall ist.
