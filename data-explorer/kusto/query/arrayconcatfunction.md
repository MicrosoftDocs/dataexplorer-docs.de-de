---
title: array_concat() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird array_concat() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c66c17ab147eb3d6c5f749e7f28fad347a50ce22
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518750"
---
# <a name="array_concat"></a>array_concat()

Verkettet eine Reihe dynamischer Arrays mit einem einzelnen Array.

**Syntax**

`array_concat(`*arr1*`[`` *arr2*, ...]`, )'

**Argumente**

* *arr1... arrN*: Geben Sie Arrays ein, die in ein dynamisches Array verkettet werden sollen. Alle Argumente müssen dynamische Arrays sein (siehe [pack_array](packarrayfunction.md)). 

**Rückgabe**

Dynamisches Array von Arrays mit arr1, arr2, ... , arrN.

**Beispiel**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|Column1|
|---|
|[1,2,4,1,2]|
|[2,4,8,2,4]|
|[3,6,12,3,6]|