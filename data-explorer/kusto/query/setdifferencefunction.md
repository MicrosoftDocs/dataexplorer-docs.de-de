---
title: set_difference() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird set_difference() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: d4edb8ec46fca99b7dd58b11bbd54442a9340c7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507802"
---
# <a name="set_difference"></a>set_difference()

Gibt `dynamic` ein Array (JSON) des Satzes aller unterschiedlichen Werte zurück, die sich im ersten Array befinden, sich aber nicht in anderen Arrays befinden - (((arr1 , arr2) , arr3) . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .

**Syntax**

`set_difference(`*arr1*`, `*arr2*`[`,` *arr3*, ...])`

**Argumente**

* *arr1... arrN*: Geben Sie Arrays ein, um einen Differenzsatz (mindestens zwei Arrays) zu erstellen. Alle Argumente müssen dynamische Arrays sein (siehe [pack_array](packarrayfunction.md)). 

**Rückgabe**

Gibt ein dynamisches Array des Satzes aller unterschiedlichen Werte zurück, die sich in arr1 befinden, sich aber nicht in anderen Arrays befinden. Siehe [`set_union()`](setunionfunction.md) [`set_intersect()`](setintersectfunction.md)und .

**Beispiel**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(x,y,w)
| project set_difference(a1, a2, a3)
```

|Column1|
|---|
|[4]|
|[8]|
|[12]|

```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|Arr|
|---|
|[]|