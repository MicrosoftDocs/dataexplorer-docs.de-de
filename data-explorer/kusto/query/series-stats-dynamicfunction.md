---
title: series_stats_dynamic() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_stats_dynamic() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: b667af6d037b0b316bf18406e1fb49528e390213
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507904"
---
# <a name="series_stats_dynamic"></a>series_stats_dynamic()

Gibt Statistiken für eine Reihe in einem dynamischen Objekt zurück.  

Die `series_stats_dynamic()` Funktion nimmt eine Spalte mit dynamischem numerischen Array als Eingabe und generiert einen dynamischen Wert mit folgendem Inhalt:
* `min`: Mindestwert im Eingabe-Array
* `min_idx`: erste Position des Minimalwerts im Eingabe-Array
* `max`: Maximalwert im Eingabe-Array
* `max_idx`: erste Position des Maximalwerts im Eingabe-Array
* `avg`: Durchschnittswert des Eingabe-Arrays
* `variance`: Beispielvarianz des Eingabe-Arrays
* `stdev`: Beispiel-Standardabweichung des Eingangsarrays

**Syntax**

`series_stats_dynamic(`*x* `[,` *ignore_nonfinite*`])`

**Argumente**

* *x*: Dynamische Arrayzelle, die ein Array numerischer Werte ist. 
* *ignore_nonfinite*: Boolean `false`(optional, default: ) Flag, das angibt, ob die Statistiken berechnet werden sollen, während nicht-endliche Werte ignoriert werden (*null*, *NaN*, *inf*, etc.). Wenn auf `false` das zurückgegebene Ergebnis festgelegt ist, `null` wenn nicht-endliche Werte im Array vorhanden sind.

**Beispiel**

```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|"min": 2.0, "min_idx": 8, "max": 87.0, "max_idx": 3, "avg": 32.8, "stdev": 28.503633853548269, "variance": 812.45714285714291