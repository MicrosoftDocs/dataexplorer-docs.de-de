---
title: series_outliers ()-Azure Daten-Explorer
description: In diesem Artikel wird series_outliers () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: c5ada33d74f5ed3e1c7b357321b23bd7a76be64e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351351"
---
# <a name="series_outliers"></a>series_outliers()

Bewertet anomaliepunkte in einer Reihe.

Die Funktion nimmt einen Ausdruck mit einem dynamischen numerischen Array als Eingabe an und generiert ein dynamisches numerisches Array mit derselben Länge. Jeder Wert des Arrays gibt mithilfe von ["Tukey es Test"](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test)eine Bewertung einer möglichen Anomalie an. Ein Wert, der größer als 1,5 im selben Element der Eingabe ist, deutet auf eine Erhöhung oder ablehnen-Anomalie hin. Ein Wert kleiner als-1,5 gibt eine Ablehnungs Anomalie an.

## <a name="syntax"></a>Syntax

`series_outliers(`*x* `, ` *Art* `, ` *ignore_val* `, ` *min_percentile* `, ` *max_percentile*`)`

## <a name="arguments"></a>Argumente

* *x*: dynamische Array Zelle, die ein Array numerischer Werte ist.
* *Art*: Algorithmus der Ausreißererkennung. Unterstützt derzeit `"tukey"` (herkömmliches "Tukey") und `"ctukey"` (benutzerdefiniertes "Tukey"). Die Standardeinstellung ist `"ctukey"`.
* *ignore_val*: numerischer Wert, der fehlende Werte in der Reihe angibt. Der Standardwert ist "Double" (null). Das Ergebnis der Nullen und Werte ignorieren ist auf festgelegt.`0`
* *min_percentile*: zum Berechnen des normalen zwischen quantifilbereichs. Der Standardwert ist 10, unterstützte benutzerdefinierte Werte liegen im Bereich `[2.0, 98.0]` ( `ctukey` nur).
* *max_percentile*: identisch, Standardwert 90, unterstützte benutzerdefinierte Werte liegen im Bereich `[2.0, 98.0]` (nur "ctukey").

In der folgenden Tabelle werden die Unterschiede zwischen `"tukey"` und beschrieben `"ctukey"` :

| Algorithmus | Quantil-Standardbereich | Unterstützt benutzerdefinierten Quantilbereich |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25%/75%              | Nein                             |
| `"ctukey"`| 10%/90%              | Ja                            |

> [!TIP]
> Die beste Möglichkeit, diese Funktion zu verwenden, besteht darin, Sie auf die Ergebnisse des Operators " [make-Series](make-seriesoperator.md) " anzuwenden.

## <a name="example"></a>Beispiel

Eine Zeitreihe mit etwas Rauschen erstellt Ausreißer. Wenn Sie diese Ausreißer (Rauschen) durch den durchschnittlichen Wert ersetzen möchten, verwenden Sie series_outliers (), um die Ausreißer zu erkennen, und ersetzen Sie Sie dann.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
