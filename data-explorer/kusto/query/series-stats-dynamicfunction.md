---
title: series_stats_dynamic ()-Azure Daten-Explorer
description: In diesem Artikel wird series_stats_dynamic () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 681e4e1b734fb9bf9d2357ec77be4ba1d61365dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245905"
---
# <a name="series_stats_dynamic"></a>series_stats_dynamic()

Gibt Statistiken für eine Reihe in einem dynamischen Objekt zurück.  

Die `series_stats_dynamic()` Funktion nimmt eine Spalte mit dynamischem numerischen Array als Eingabe an und generiert einen dynamischen Wert mit folgendem Inhalt:
* `min`: Minimalwert im Eingabe Array
* `min_idx`: erste Position des minimal Werts im Eingabe Array
* `max`: maximaler Wert im Eingabe Array
* `max_idx`: erste Position des maximalen Werts im Eingabe Array
* `avg`: Durchschnittlicher Wert des Eingabe Arrays
* `variance`: Stichproben Varianz des Eingabe Arrays
* `stdev`: Stichproben Standardabweichung des Eingabe Arrays

## <a name="syntax"></a>Syntax

`series_stats_dynamic(`*x* - `[,` *ignore_nonfinite*`])`

## <a name="arguments"></a>Argumente

* *x*: dynamische Array Zelle, bei der es sich um ein Array von numerischen Werten handelt. 
* *ignore_nonfinite*: boolesches Flag (optional, Standard: `false` ), das angibt, ob die Statistiken beim ignorieren von nicht begrenzten Werten (*null*, *NaN*, *INF*usw.) berechnet werden sollen. Wenn auf `false` das zurückgegebene Ergebnis festgelegt ist, ist dies der `null` Wert, wenn nicht endliche Werte im Array vorhanden sind.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|{"min": 2,0, "min_idx": 8, "Max": 87,0, "max_idx": 3, "AVG": 32,8, "StDev": 28.503633853548269, "Varianz": 812.45714285714291}
