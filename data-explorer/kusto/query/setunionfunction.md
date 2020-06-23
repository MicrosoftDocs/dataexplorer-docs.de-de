---
title: set_union ()-Azure Daten-Explorer
description: In diesem Artikel wird set_union () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 19c7b47318d0748510aba146968149cd0eb246a2
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264571"
---
# <a name="set_union"></a>set_union()

Gibt ein `dynamic` Array der Menge aller unterschiedlichen Werte zurück, die in einem der Arrays vorhanden sind (Arr1 ∪ arr2 ∪...).

**Syntax**

`set_union(`*Arr1* `, ` *arr2* `[` ,` *arr3*, ...]``)`

**Argumente**

* *Arr1... arrn*: Eingabe Arrays zum Erstellen eines Union-Satzes (mindestens zwei Arrays). Alle Argumente müssen dynamische Arrays sein (siehe [pack_array](packarrayfunction.md)). 

**Rückgabe**

Gibt ein dynamisches Array der Menge aller unterschiedlichen Werte zurück, die in einem der Arrays vorhanden sind. Siehe [`set_intersect()`](setintersectfunction.md) und [`set_difference()`](setdifferencefunction.md) .

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w)
| project set_union(a1, a2, a3)
```

|Column1|
|---|
|[1, 2, 4, 8]|
|[2, 4, 8, 16]|
|[3, 6, 12, 24]|
