---
title: series_add ()-Azure Daten-Explorer
description: In diesem Artikel wird series_add () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b5847fec10eb76a6fe5a139809766d2a3ca4f089
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372917"
---
# <a name="series_add"></a>series_add()

Berechnet die Element Weise Addition von zwei numerischen Reihen Eingaben.

**Syntax**

`series_add(`*Series1* `,` *series2*`)`

**Argumente**

* *Series1, series2*: numerische Eingabe Arrays, die Element Weise in ein dynamisches Array Ergebnis eingefügt werden. Alle Argumente müssen dynamische Arrays sein. 

**Rückgabe**

Dynamisches Array des berechneten Element weisen Hinzufügevorgangs zwischen den beiden Eingaben. Ein nicht numerisches Element oder ein nicht vorhandenes Element (Arrays mit unterschiedlichen Größen) ergeben einen- `null` Elementwert.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_add_s2 = series_add(s1, s2)
```

|s1|s2|s1_add_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[5, 4, 5]|
|[2, 4, 8]|[8, 4, 2]|[10, 8, 10]|
|[3, 6, 12]|[12, 6, 3]|[15, 12, 15]|
