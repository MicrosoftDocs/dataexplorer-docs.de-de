---
title: series_not_equals ()-Azure Daten-Explorer
description: In diesem Artikel wird series_not_equals () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 7b17d9b7150d6d58ae3b3b3be7abf83dc9979038
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372553"
---
# <a name="series_not_equals"></a>series_not_equals()

Berechnet die Element Weise not Gleichheits ( `!=` )-Logik Operation von zwei numerischen Reihen Eingaben.

**Syntax**

`series_not_equals (`*Series1* `,` *Series2*`)`

**Argumente**

* *Series1, series2*: numerische Eingabe Arrays, die Element Weise verglichen werden sollen. Alle Argumente müssen dynamische Arrays sein. 

**Rückgabe**

Dynamisches Array von booleschen Werten, das die berechnete Element Weise Not Equal Logic-Operation zwischen den beiden Eingaben enthält. Ein nicht numerisches Element oder ein nicht vorhandenes Element (Arrays mit unterschiedlichen Größen) ergeben einen- `null` Elementwert.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_not_equals_s2 = series_not_equals(s1, s2)
```

|s1|s2|s1_not_equals_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[true, false, true]|

**Siehe auch**

Eine ganze Reihe von Statistik vergleichen finden Sie unter:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
