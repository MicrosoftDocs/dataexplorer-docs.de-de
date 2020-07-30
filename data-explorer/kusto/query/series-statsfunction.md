---
title: series_stats ()-Azure Daten-Explorer
description: In diesem Artikel wird series_stats () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 69e09ec9528e99e250986657bf5d7cc0756b14f8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351249"
---
# <a name="series_stats"></a>series_stats()

`series_stats()`gibt Statistiken für eine Reihe in mehreren Spalten zurück.  

Die `series_stats()` Funktion nimmt eine Spalte mit dynamischem numerischen Array als Eingabe an und berechnet die folgenden Spalten:
* `min`: Minimalwert im Eingabe Array
* `min_idx`: erste Position des minimal Werts im Eingabe Array
* `max`: maximaler Wert im Eingabe Array
* `max_idx`: erste Position des maximalen Werts im Eingabe Array
* `avg`: Durchschnittlicher Wert des Eingabe Arrays
* `variance`: Stichproben Varianz des Eingabe Arrays
* `stdev`: Stichproben Standardabweichung des Eingabe Arrays

> [!NOTE] 
> Diese Funktion gibt mehrere Spalten zurück, sodass Sie nicht als Argument für eine andere Funktion verwendet werden kann.

## <a name="syntax"></a>Syntax

Project `series_stats(` *x* `[,` *ignore_nonfinite* `])` oder Erweiterung `series_stats(` *x* `)` gibt alle oben erwähnten Spalten mit den folgenden Namen zurück: series_stats_x_min, series_stats_x_min_idx und usw.
 
Project (m, Mi) = `series_stats(` *x* `)` oder Extend (m, Mi) = `series_stats(` *x* `)` gibt die folgenden Spalten zurück: m (min) und Mi (min_idx).

## <a name="arguments"></a>Argumente

* *x*: dynamische Array Zelle, bei der es sich um ein Array von numerischen Werten handelt. 
* *ignore_nonfinite*: boolesches Flag (optional, Standard: `false` ), das angibt, ob die Statistiken beim ignorieren von nicht begrenzten Werten (*null*, *NaN*, *INF*usw.) berechnet werden sollen. Wenn der Wert auf festgelegt `false` ist, sind die zurückgegebenen Werte, `null` Wenn nicht endliche Werte im Array vorhanden sind.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project series_stats(x)

```

|series_stats_x_min|series_stats_x_min_idx|series_stats_x_max|series_stats_x_max_idx|series_stats_x_avg|series_stats_x_stdev|series_stats_x_variance|
|---|---|---|---|---|---|---|
|2|8|87|3|32,8|28.5036338535483|812.457142857143|
