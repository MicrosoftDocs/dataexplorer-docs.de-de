---
title: set_difference ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird set_difference () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 7e13a9b652e1bdadb325cd866bddd78761b25b85
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372396"
---
# <a name="set_difference"></a>set_difference()

Gibt ein `dynamic` -Array (JSON) der Menge aller unterschiedlichen Werte zurück, die sich im ersten Array befinden, jedoch nicht in anderen Arrays (((Arr1 \ arr2) \ arr3) \...).

**Syntax**

`set_difference(`*Arr1* `, ` *arr2* `[` ,` *arr3*, ...])`

**Argumente**

* *Arr1... arrn*: Eingabe Arrays zum Erstellen einer Differenz Menge (mindestens zwei Arrays). Alle Argumente müssen dynamische Arrays sein (siehe [pack_array](packarrayfunction.md)). 

**Rückgabe**

Gibt ein dynamisches Array der Menge aller unterschiedlichen Werte zurück, die in Arr1, aber nicht in anderen Arrays sind. Siehe [`set_union()`](setunionfunction.md) und [`set_intersect()`](setintersectfunction.md) .

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|88|
|12.12.2016|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|r|
|---|
|[]|