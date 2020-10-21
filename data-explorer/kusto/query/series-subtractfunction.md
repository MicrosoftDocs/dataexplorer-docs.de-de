---
title: series_subtract ()-Azure Daten-Explorer
description: In diesem Artikel wird series_subtract () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 09d208b25096afa58bd9f673d0103b30f7e8bb47
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241956"
---
# <a name="series_subtract"></a>series_subtract()

Berechnet die Element Weise Subtraktion von zwei numerischen Reihen Eingaben.

## <a name="syntax"></a>Syntax

`series_subtract(`*Series1* `,` *series2*`)`

## <a name="arguments"></a>Argumente

* *Series1, series2*: numerische Eingabe Arrays, die zweite Element Weise, die vom ersten in ein dynamisches Array Ergebnis subtrahiert wird. Alle Argumente müssen dynamische Arrays sein. 

## <a name="returns"></a>Rückgabe

Dynamisches Array des berechneten Element weisen subtrahieren-Vorgangs zwischen den beiden Eingaben. Ein nicht numerisches Element oder ein nicht vorhandenes Element (Arrays mit unterschiedlichen Größen) ergeben einen- `null` Elementwert.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_subtract_s2 = series_subtract(s1, s2)
```

|s1|s2|s1_subtract_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[-3, 0, 3]|
|[2, 4, 8]|[8, 4, 2]|[-6, 0,0]|
|[3, 6, 12]|[12, 6, 3]|[-9, 0, 9]|
