---
title: array_rotate_left() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird array_rotate_left() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 82eb8c24a3ed04146e5416b020bda7085426d138
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518818"
---
# <a name="array_rotate_left"></a>array_rotate_left()

`array_rotate_left()`dreht Werte innerhalb eines Arrays nach links.

**Syntax**

`array_rotate_left(`*arr*, *rotate_count*`)`

**Argumente**

* *arr*: Eingabe-Array zu teilen, muss dynamisches Array sein.
* *rotate_count*: Ganzzahl, die die Anzahl der Positionen angibt, die Arrayelemente nach links gedreht werden. Wenn der Wert negativ ist, werden die Elemente nach rechts gedreht.

**Rückgabe**

Dynamisches Array, das die gleiche Anzahl der Elemente enthält wie im ursprünglichen Array, wobei jedes Element gemäß *rotate_count*gedreht wurde.

**Siehe auch**

* Informationen zum Drehen des Arrays nach rechts finden Sie unter [array_rotate_right()](array_rotate_rightfunction.md).
* Informationen zum Verschieben des Arrays nach links finden [Sie unter array_shift_left()](array_shift_leftfunction.md).
* Informationen zum Verschieben des Arrays nach rechts finden [Sie unter array_shift_right()](array_shift_rightfunction.md).

**Beispiele**

* Drehen nach links durch zwei Positionen:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, 2)
    ```
    
    |Arr|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,1,2]|

* Drehen nach rechts um zwei Positionen mit negativem rotate_count Wert:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, -2)
    ```
    
    |Arr|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[4,5,1,2,3]|