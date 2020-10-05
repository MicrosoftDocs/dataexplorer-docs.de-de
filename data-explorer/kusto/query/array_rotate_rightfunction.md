---
title: array_rotate_right ()-Azure Daten-Explorer
description: In diesem Artikel wird array_rotate_right () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 267e2b4d5769efee87dd90b9a3265310998fcbd9
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712101"
---
# <a name="array_rotate_right"></a>array_rotate_right()

Rotiert Werte innerhalb eines `dynamic` Arrays nach rechts.

## <a name="syntax"></a>Syntax

`array_rotate_right(`*arr*, *rotate_count*`)`

## <a name="arguments"></a>Argumente

* *arr*: das zu teilende Eingabe Array muss ein dynamisches Array sein.
* *rotate_count*: ganze Zahl, die die Anzahl der Positionen angibt, die Array Elemente nach rechts gedreht werden. Wenn der Wert negativ ist, werden die Elemente nach links gedreht.

## <a name="returns"></a>Rückgabe

Dynamisches Array, das die gleiche Menge an Elementen wie im ursprünglichen Array enthält, wobei jedes Element gemäß *rotate_count*gedreht wurde.

## <a name="see-also"></a>Weitere Informationen

* Informationen zum Drehen des Arrays nach Links finden Sie unter [array_rotate_left ()](array_rotate_leftfunction.md).
* Informationen zum Verschieben des Arrays nach Links finden Sie unter [array_shift_left ()](array_shift_leftfunction.md).
* Informationen zum Verschieben des Arrays nach rechts finden Sie unter [array_shift_right ()](array_shift_rightfunction.md).

## <a name="examples"></a>Beispiele

* Drehen nach rechts um zwei Positionen:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, 2)
    ```
    
    |r|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[4, 5, 1, 2, 3]|

* Drehen nach links um zwei Positionen mit negativem rotate_count Wert:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, -2)
    ```
    
    |r|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5, 1, 2]|
