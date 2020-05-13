---
title: series_divide ()-Azure Daten-Explorer
description: In diesem Artikel wird series_divide () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7d5bdba030687c17c355eb72ce2fc9c358c10ebd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372845"
---
# <a name="series_divide"></a>series_divide()

Berechnet die Element Weise Division von zwei numerischen Reihen Eingaben.

**Syntax**

`series_divide(`*Series1* `,` *series2*`)`

**Argumente**

* *Series1, series2*: numerische Eingabe Arrays, der erste, der Element Weise dividiert durch den zweiten in ein dynamisches Array Ergebnis ist. Alle Argumente müssen dynamische Arrays sein. 

**Rückgabe**

Dynamisches Array des berechneten Element weisen Teilungs Vorgangs zwischen den beiden Eingaben. Ein nicht numerisches Element oder ein nicht vorhandenes Element (Arrays mit unterschiedlichen Größen) ergeben einen- `null` Elementwert.

Hinweis: die Ergebnis Reihe ist vom Typ "Double", auch wenn die Eingaben ganze Zahlen sind. Division durch 0 (null) folgt der doppelten Division durch NULL (z. b. 2/0 ergibt Double (+ INF)).

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1, 2, 4]    |[4, 2, 1]|   [0,25, 1.0, 4.0]|
|[2, 4, 8]    |[8, 4, 2]|   [0,25, 1.0, 4.0]|
|[3, 6, 12]   |[12, 6, 3]|  [0,25, 1.0, 4.0]|
