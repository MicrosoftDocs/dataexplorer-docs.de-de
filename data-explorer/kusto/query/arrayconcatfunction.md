---
title: array_concat ()-Azure Daten-Explorer
description: In diesem Artikel wird array_concat () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4c8e2da4d2ba4ed205987b5a1d063ac2e75ed289
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246917"
---
# <a name="array_concat"></a>array_concat()

Verkettet eine Reihe dynamischer Arrays mit einem einzelnen Array.

## <a name="syntax"></a>Syntax

`array_concat(`*Arr1* `[` , ` *arr2*, ...]` ) '

## <a name="arguments"></a>Argumente

* *Arr1... arrn*: Eingabe Arrays, die in ein dynamisches Array verkettet werden sollen. Alle Argumente müssen dynamische Arrays sein (siehe [pack_array](packarrayfunction.md)). 

## <a name="returns"></a>Rückgabe

Dynamisches Array von Arrays mit Arr1, arr2,..., arrn.

## <a name="example"></a>Beispiel

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
