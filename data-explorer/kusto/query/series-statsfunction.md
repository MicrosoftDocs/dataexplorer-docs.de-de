---
title: series_stats() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_stats() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 07aa5df7351a5d4be1522d39456423197bde508d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507921"
---
# <a name="series_stats"></a>series_stats()

`series_stats()`gibt Statistiken für eine Reihe in mehreren Spalten zurück.  

Die `series_stats()` Funktion nimmt eine Spalte mit dynamischem numerischen Array als Eingabe und berechnet die folgenden Spalten:
* `min`: Mindestwert im Eingabe-Array
* `min_idx`: erste Position des Minimalwerts im Eingabe-Array
* `max`: Maximalwert im Eingabe-Array
* `max_idx`: erste Position des Maximalwerts im Eingabe-Array
* `avg`: Durchschnittswert des Eingabe-Arrays
* `variance`: Beispielvarianz des Eingabe-Arrays
* `stdev`: Beispiel-Standardabweichung des Eingangsarrays

> [!NOTE] 
> Diese Funktion gibt mehrere Spalten zurück, sodass sie nicht als Argument für eine andere Funktion verwendet werden kann.

**Syntax**

project `series_stats(` *x* `[,` *ignore_nonfinite* `])` oder `series_stats(`extend *x* `)` Gibt alle oben genannten Spalten mit den folgenden Namen zurück: series_stats_x_min, series_stats_x_min_idx usw.
 
projekt (m,`series_stats(`mi)=*x* `)` or extend`series_stats(`(m, mi)=*x* `)` Gibt die folgenden Spalten zurück: m (min) und mi (min_idx).

**Argumente**

* *x*: Dynamische Arrayzelle, die ein Array numerischer Werte ist. 
* *ignore_nonfinite*: Boolean `false`(optional, default: ) Flag, das angibt, ob die Statistiken berechnet werden sollen, während nicht-endliche Werte ignoriert werden (*null*, *NaN*, *inf*, etc.). Wenn auf `false`gesetzt, werden `null` die zurückgegebenen Werte angezeigt, wenn nicht endliche Werte im Array vorhanden sind.

**Beispiel**

```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project series_stats(x)

```

|series_stats_x_min|series_stats_x_min_idx|series_stats_x_max|series_stats_x_max_idx|series_stats_x_avg|series_stats_x_stdev|series_stats_x_variance|
|---|---|---|---|---|---|---|
|2|8|87|3|32,8|28.5036338535483|812.457142857143|
