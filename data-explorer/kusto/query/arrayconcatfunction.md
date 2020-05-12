---
title: array_concat ()-Azure Daten-Explorer
description: In diesem Artikel wird array_concat () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 681178cc12d145b1c574357e87ae4f7b33d736c4
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225648"
---
# <a name="array_concat"></a>array_concat()

Verkettet eine Reihe dynamischer Arrays mit einem einzelnen Array.

**Syntax**

`array_concat(`*Arr1* `[` , ` *arr2*, ...]` ) '

**Argumente**

* *Arr1... arrn*: Eingabe Arrays, die in ein dynamisches Array verkettet werden sollen. Alle Argumente müssen dynamische Arrays sein (siehe [pack_array](packarrayfunction.md)). 

**Rückgabe**

Dynamisches Array von Arrays mit Arr1, arr2,..., arrn.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|Column1|
|---|
|[1, 2, 4, 1, 2]|
|[2, 4, 8, 2, 4]|
|[3, 6, 12, 3, 6]|
