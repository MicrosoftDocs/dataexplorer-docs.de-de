---
title: series_not_equals ()-Azure Daten-Explorer
description: In diesem Artikel wird series_not_equals () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: c55cd4c4846f568dccc7f17e11e024f97c87f14b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246044"
---
# <a name="series_not_equals"></a>series_not_equals()

Berechnet die Element Weise not Gleichheits ( `!=` )-Logik Operation von zwei numerischen Reihen Eingaben.

## <a name="syntax"></a>Syntax

`series_not_equals (`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Argumente

* *Series1, series2*: numerische Eingabe Arrays, die Element Weise verglichen werden sollen. Alle Argumente müssen dynamische Arrays sein. 

## <a name="returns"></a>Rückgabe

Dynamisches Array von booleschen Werten, das die berechnete Element Weise Not Equal Logic-Operation zwischen den beiden Eingaben enthält. Ein nicht numerisches Element oder ein nicht vorhandenes Element (Arrays mit unterschiedlichen Größen) ergeben einen- `null` Elementwert.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_not_equals_s2 = series_not_equals(s1, s2)
```

|s1|s2|s1_not_equals_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[true, false, true]|

## <a name="see-also"></a>Weitere Informationen

Eine ganze Reihe von Statistik vergleichen finden Sie unter:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
