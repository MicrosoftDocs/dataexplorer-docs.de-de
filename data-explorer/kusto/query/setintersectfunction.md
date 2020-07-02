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
ms.openlocfilehash: 6739b01f996c04829a1fdb78eab96f6e51b011bb
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763661"
---
# <a name="set_intersect"></a>set_intersect()

Gibt ein `dynamic` Array der Menge aller unterschiedlichen Werte zurück, die sich in allen Arrays befinden-(Arr1 ∩ arr2 ∩...).

**Syntax**

`set_intersect(`*Arr1* `, ` *arr2* `[` ,` *arr3*, ...])`

**Argumente**

* *Arr1... arrn*: Eingabe Arrays zum Erstellen eines Intersect-Satzes (mindestens zwei Arrays). Alle Argumente müssen dynamische Arrays sein. Weitere Informationen finden Sie unter [pack_array](packarrayfunction.md). 

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
| [1]|
|2,2|
|[3]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|r|
|---|
|[]|
