---
title: series_outliers ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird series_outliers () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 16e82ec68a463b97699f7d02e18c46df65221c7b
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618654"
---
# <a name="series_outliers"></a>series_outliers()

Bewertet anomaliepunkte in einer Reihe.

Nimmt einen Ausdruck, der das dynamische numerische Array enthält, als Eingabe und generiert ein dynamisches numerisches Array mit derselben Länge. Jeder Wert des Arrays gibt eine Bewertung möglicher Anomalien mit [dem Tukey-Test](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test)an. Ein Wert, der höher als 1,5 oder kleiner als -1,5 ist, weist auf einen Anstieg bzw. einen Abfall der Anomalie in demselben Element der Eingabe hin.   

**Syntax**

`series_outliers(`*x*`, `*kind*`, `*max_percentile* *min_percentile**ignore_val*x-ignore_val`, `min_percentile max_percentile`, ``)`

**Argumente**

* *x*: dynamische Array Zelle, bei der es sich um ein Array von numerischen Werten handelt.
* *Art*: Algorithmus der Ausreißererkennung. Unterstützt `"tukey"` derzeit (herkömmlicher Tukey `"ctukey"` ) und (benutzerdefinierter Tukey). Die Standardeinstellung ist `"ctukey"`.
* *ignore_val*: numerischer Wert, der fehlende Werte in der Reihe anzeigt. der Standardwert ist "Double" (null). Das Ergebnis der Nullen und Werte ignorieren ist auf `0`festgelegt.
* *min_percentile*: zur Berechnung des normalen zwischen quantilbereichs beträgt der Standardwert 10, unterstützte benutzerdefinierte Werte liegen im `[2.0, 98.0]` Bereich`ctukey` (nur). 
* *max_percentile*: identisch, Standardwert 90, unterstützte benutzerdefinierte Werte liegen `[2.0, 98.0]` im Bereich (nur "ctukey"). 

In der folgenden Tabelle werden die `"tukey"` unter `"ctukey"`schiede zwischen und beschrieben:

| Algorithmus | Quantil-Standardbereich | Unterstützt benutzerdefinierten Quantilbereich |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25%/75%              | Nein                             |
| `"ctukey"`| 10%/90%              | Ja                            |


> [!TIP]
> Die einfachste Methode, diese Funktion zu verwenden, ist die Anwendung auf die Ergebnisse des Operators " [make-Series](make-seriesoperator.md) ".

**Beispiel**

Angenommen, Sie haben eine Zeitreihe mit einem Rausch, der Ausreißer erstellt, und Sie können diese Ausreißer (Rauschen) durch den durchschnittlichen Wert ersetzen, indem Sie series_outliers () verwenden, um die Ausreißer zu erkennen und Sie dann zu ersetzen:

```kusto
range x from 1 to 100 step 1 
| extend y=iff(x==20 or x==80, 10*rand()+10+(50-x)/2, 10*rand()+10) // generate a sample series with outliers at x=20 and x=80
| summarize x=make_list(x),series=make_list(y)
| extend series_stats(series), outliers=series_outliers(series)
| mv-expand x to typeof(long), series to typeof(double), outliers to typeof(double)
| project x, series , outliers_removed=iff(outliers > 1.5 or outliers < -1.5, series_stats_series_avg , series ) // replace outliers with the average
| render linechart
``` 

:::image type="content" source="images/series-outliersfunction/series-outliers.png" alt-text="Reihen Ausreißern" border="false":::
