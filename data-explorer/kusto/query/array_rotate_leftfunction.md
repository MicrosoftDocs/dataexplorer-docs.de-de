---
title: array_rotate_left ()-Azure Daten-Explorer
description: In diesem Artikel wird array_rotate_left () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: ae28848ee46a4313ac2f24fb8a796cd0ced3ba4d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225818"
---
# <a name="array_rotate_left"></a>array_rotate_left()

`array_rotate_left()`rotiert Werte in einem Array nach links.

**Syntax**

`array_rotate_left(`*arr*, *rotate_count*`)`

**Argumente**

* *arr*: das zu teilende Eingabe Array muss ein dynamisches Array sein.
* *rotate_count*: ganze Zahl, die die Anzahl der Positionen angibt, die Array Elemente nach links gedreht werden. Wenn der Wert negativ ist, werden die Elemente nach rechts gedreht.

**Rückgabe**

Dynamisches Array, das die gleiche Menge an Elementen wie im ursprünglichen Array enthält, wobei jedes Element gemäß *rotate_count*gedreht wurde.

**Siehe auch**

* Informationen zum Drehen des Arrays nach rechts finden Sie unter [array_rotate_right ()](array_rotate_rightfunction.md).
* Informationen zum Verschieben des Arrays nach Links finden Sie unter [array_shift_left ()](array_shift_leftfunction.md).
* Informationen zum Verschieben des Arrays nach rechts finden Sie unter [array_shift_right ()](array_shift_rightfunction.md).

**Beispiele**

* Drehen nach links um zwei Positionen:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, 2)
    ```
    
    |r|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5, 1, 2]|

* Drehen nach rechts um zwei Positionen mit negativem rotate_count Wert:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, -2)
    ```
    
    |r|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[4, 5, 1, 2, 3]|