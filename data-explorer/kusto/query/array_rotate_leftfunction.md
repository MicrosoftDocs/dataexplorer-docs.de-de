---
title: array_rotate_left ()-Azure Daten-Explorer
description: In diesem Artikel wird array_rotate_left () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 7c9e6a318ebcd0a0911b3a86c1bf0ad99aeb2f51
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248128"
---
# <a name="array_rotate_left"></a>array_rotate_left()

Rotiert Werte in einem `dynamic` Array nach links.

## <a name="syntax"></a>Syntax

`array_rotate_left(`*arr*, *rotate_count*`)`

## <a name="arguments"></a>Argumente

* *arr*: das zu teilende Eingabe Array muss ein dynamisches Array sein.
* *rotate_count*: ganze Zahl, die die Anzahl der Positionen angibt, die Array Elemente nach links gedreht werden. Wenn der Wert negativ ist, werden die Elemente nach rechts gedreht.

## <a name="returns"></a>Rückgabe

Dynamisches Array, das die gleiche Menge an Elementen wie im ursprünglichen Array enthält, wobei jedes Element gemäß *rotate_count*gedreht wurde.

## <a name="see-also"></a>Weitere Informationen

* Informationen zum Drehen des Arrays nach rechts finden Sie unter [array_rotate_right ()](array_rotate_rightfunction.md).
* Informationen zum Verschieben des Arrays nach Links finden Sie unter [array_shift_left ()](array_shift_leftfunction.md).
* Informationen zum Verschieben des Arrays nach rechts finden Sie unter [array_shift_right ()](array_shift_rightfunction.md).

## <a name="examples"></a>Beispiele

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