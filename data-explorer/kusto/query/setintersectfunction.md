---
title: set_intersect ()-Azure Daten-Explorer
description: In diesem Artikel wird set_intersect () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 682cff2dc5a4334a4543767048429b1ea04dc329
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242444"
---
# <a name="set_intersect"></a>set_intersect()

Gibt ein `dynamic` Array der Menge aller unterschiedlichen Werte zurück, die sich in allen Arrays befinden-(Arr1 ∩ arr2 ∩...).

## <a name="syntax"></a>Syntax

`set_intersect(`*Arr1* `, ` *arr2* `[` ,` *arr3*, ...])`

## <a name="arguments"></a>Argumente

* *Arr1... arrn*: Eingabe Arrays zum Erstellen eines Intersect-Satzes (mindestens zwei Arrays). Alle Argumente müssen dynamische Arrays sein. Weitere Informationen finden Sie unter [pack_array](packarrayfunction.md). 

## <a name="returns"></a>Rückgabe

Gibt ein dynamisches Array der Menge aller unterschiedlichen Werte zurück, die in allen Arrays vorliegen. Siehe [`set_union()`](setunionfunction.md) und [`set_difference()`](setdifferencefunction.md) .

## <a name="example"></a>Beispiel

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
|€|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|r|
|---|
|[]|
