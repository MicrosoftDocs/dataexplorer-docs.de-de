---
title: set_intersect ()-Azure Daten-Explorer
description: In diesem Artikel wird set_intersect () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 23b751dce38f5b595ba081c9a29e1b1a5442c96f
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372357"
---
# <a name="set_intersect"></a>set_intersect()

Gibt ein `dynamic` -Array (JSON) der Menge aller unterschiedlichen Werte zurück, die sich in allen Arrays befinden-(Arr1 ∩ arr2 ∩...).

**Syntax**

`set_intersect(`*Arr1* `, ` *arr2* `[` ,` *arr3*, ...])`

**Argumente**

* *Arr1... arrn*: Eingabe Arrays zum Erstellen eines Intersect-Satzes (mindestens zwei Arrays). Alle Argumente müssen dynamische Arrays sein (siehe [pack_array](packarrayfunction.md)). 

**Rückgabe**

Gibt ein dynamisches Array der Menge aller unterschiedlichen Werte zurück, die in allen Arrays vorliegen. Siehe [`set_union()`](setunionfunction.md) und [`set_difference()`](setdifferencefunction.md) .

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w,x)
| project set_intersect(a1, a2, a3)
```

|Column1|
|---|
|[1]|
|2,2|
|€|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|r|
|---|
|[]|
