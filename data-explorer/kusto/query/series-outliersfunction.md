---
title: series_outliers() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_outliers() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 47e479ce7fe09b2456405ac3f7daed4721374f32
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663458"
---
# <a name="series_outliers"></a>series_outliers()

Erzielt Anomaliepunkte in einer Serie.

Nimmt einen Ausdruck, der dynamische numerische Arrays enthält, als Eingabe und generiert ein dynamisches numerisches Array gleicher Länge. Jeder Wert des Arrays gibt eine Bewertung möglicher Anomalien mit [Tukeys Test](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test)an. Ein Wert, der höher als 1,5 oder kleiner als -1,5 ist, weist auf einen Anstieg bzw. einen Abfall der Anomalie in demselben Element der Eingabe hin.   

**Syntax**

`series_outliers(`*x*`, `*art*`, `*ignore_val*`, `*min_percentile*`, `*max_percentile*`)`

**Argumente**

* *x*: Dynamische Arrayzelle, die ein Array numerischer Werte ist
* *Art*: Algorithmus der Ausreißererkennung. Derzeit `"tukey"` unterstützt (traditionelle Tukey) und `"ctukey"` (benutzerdefinierte Tukey). Die Standardeinstellung ist `"ctukey"`.
* *ignore_val*: numerischer Wert, der fehlende Werte in der Reihe angibt, ist der Standardwert double(null). Die Punktzahl von NULL- und `0`Ignorieren-Werten ist auf festgelegt.
* *min_percentile*: Für die Berechnung des normalen Interquantilbereichs ist der Standardwert 10, die unterstützten benutzerdefinierten Werte liegen im Bereich `[2.0, 98.0]` (nur)`ctukey` 
* *max_percentile*: derselbe Standard ist 90, unterstützte `[2.0, 98.0]` benutzerdefinierte Werte befinden sich im Bereich (nur ctukey) 

In der folgenden `"tukey"` Tabelle `"ctukey"`werden die Unterschiede zwischen und:

| Algorithmus | Quantil-Standardbereich | Unterstützt benutzerdefinierten Quantilbereich |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25%/75%              | Nein                             |
| `"ctukey"`| 10%/90%              | Ja                            |


> [!TIP]
> Der bequemste Weg, diese Funktion zu verwenden, ist die Anwendung auf die Ergebnisse des [Make-Series-Operators.](make-seriesoperator.md)

**Beispiel**

Angenommen, Sie haben eine Zeitreihe mit einigen Rauschen, die Ausreißer erzeugt, und Sie möchten diese Ausreißer (Rauschen) durch den Durchschnittswert ersetzen, Sie könnten series_outliers() verwenden, um die Ausreißer zu erkennen und sie dann zu ersetzen:

```kusto
range x from 1 to 100 step 1 
| extend y=iff(x==20 or x==80, 10*rand()+10+(50-x)/2, 10*rand()+10) // generate a sample series with outliers at x=20 and x=80
| summarize x=make_list(x),series=make_list(y)
| extend series_stats(series), outliers=series_outliers(series)
| mv-expand x to typeof(long), series to typeof(double), outliers to typeof(double)
| project x, series , outliers_removed=iff(outliers > 1.5 or outliers < -1.5, series_stats_series_avg , series ) // replace outliers with the average
| render linechart
``` 

:::image type="content" border="false" source="images/samples/series-outliers.png" alt-text="Serienausreißer":::
