---
title: series_less ()-Azure Daten-Explorer
description: In diesem Artikel wird series_less () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ccf5638c2393ca4fbd63474d93c60d96972580fd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251543"
---
# <a name="series_less"></a>series_less()

Berechnet die Element Weise Less ( `<` )-Logik Operation von zwei numerischen Reihen Eingaben.

## <a name="syntax"></a>Syntax

`series_less (`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Argumente

* *Series1, series2*: numerische Eingabe Arrays, die Element Weise verglichen werden sollen. Alle Argumente müssen dynamische Arrays sein. 

## <a name="returns"></a>Rückgabe

Dynamisches Array von booleschen Werten, das die berechnete Element Weise weniger Logik Operation zwischen den beiden Eingaben enthält. Ein nicht numerisches Element oder ein nicht vorhandenes Element (Arrays mit unterschiedlichen Größen) ergeben einen- `null` Elementwert.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_s2 = series_less(s1, s2)
```

|s1|s2|s1_less_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[true, false, false]|

## <a name="see-also"></a>Weitere Informationen

Eine ganze Reihe von Statistik vergleichen finden Sie unter:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
