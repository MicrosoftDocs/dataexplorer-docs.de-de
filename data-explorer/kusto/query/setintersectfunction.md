---
title: set_intersect() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird set_intersect() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 0a1ef86573a408f644e26b3b23f0db42e327573a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507751"
---
# <a name="set_intersect"></a>set_intersect()

Gibt `dynamic` ein (JSON) Array des Satzes aller unterschiedlichen Werte zurück, die sich in allen Arrays befinden - (arr1, arr2x ...).

**Syntax**

`set_intersect(`*arr1*`, `*arr2*`[`,` *arr3*, ...])`

**Argumente**

* *arr1... arrN*: Geben Sie Arrays ein, um einen Intersect-Satz (mindestens zwei Arrays) zu erstellen. Alle Argumente müssen dynamische Arrays sein (siehe [pack_array](packarrayfunction.md)). 

**Rückgabe**

Gibt ein dynamisches Array des Satzes aller unterschiedlichen Werte zurück, die sich in allen Arrays befinden. Siehe [`set_union()`](setunionfunction.md) [`set_difference()`](setdifferencefunction.md)und .

**Beispiel**

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
|[2]|
|[3]|

```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|Arr|
|---|
|[]|