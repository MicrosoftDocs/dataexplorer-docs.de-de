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
ms.openlocfilehash: 8aec2bdebacc1bfd87b84bbfc83a6aed5cb05427
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351130"
---
# <a name="set_union"></a>set_union()

Gibt ein `dynamic` Array der Menge aller unterschiedlichen Werte zurück, die in einem der Arrays vorhanden sind (Arr1 ∪ arr2 ∪...).

## <a name="syntax"></a>Syntax

`set_union(`*Arr1* `, ` *arr2* `[` ,` *arr3*, ...]``)`

## <a name="arguments"></a>Argumente

* *Arr1... arrn*: Eingabe Arrays zum Erstellen eines Union-Satzes (mindestens zwei Arrays). Alle Argumente müssen dynamische Arrays sein (siehe [pack_array](packarrayfunction.md)). 

## <a name="returns"></a>Gibt zurück

Gibt ein dynamisches Array der Menge aller unterschiedlichen Werte zurück, die in einem der Arrays vorhanden sind. Siehe [`set_intersect()`](setintersectfunction.md) und [`set_difference()`](setdifferencefunction.md) .

## <a name="example"></a>Beispiel

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
