---
title: set_union() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden set_union() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: de9220ea6f9e23e458dd379fb164317842a48c94
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507666"
---
# <a name="set_union"></a>set_union()

Gibt `dynamic` ein (JSON) Array des Satzes aller unterschiedlichen Werte zurück, die sich in einem arrays befinden - (arr1, arr2x ...).

**Syntax**

`set_union(`*arr1*`, `*arr2*`[`,` *arr3*, ...]``)`

**Argumente**

* *arr1... arrN*: Geben Sie Arrays ein, um einen Union-Satz (mindestens zwei Arrays) zu erstellen. Alle Argumente müssen dynamische Arrays sein (siehe [pack_array](packarrayfunction.md)). 

**Rückgabe**

Gibt ein dynamisches Array des Satzes aller unterschiedlichen Werte zurück, die sich in einem Array befinden. Siehe [`set_intersect()`](setintersectfunction.md) [`set_difference()`](setdifferencefunction.md)und .

**Beispiel**

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
|[1,2,4,8]|
|[2,4,8,16]|
|[3,6,12,24]|