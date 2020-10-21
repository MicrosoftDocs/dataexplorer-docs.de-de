---
title: array_shift_left ()-Azure Daten-Explorer
description: In diesem Artikel wird array_shift_left () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 4bdadd276a59b30ed347a3e293eb5e5c7831063b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247026"
---
# <a name="array_shift_left"></a>array_shift_left()

Verschiebt die Werte in einem- `dynamic` Array nach links.

## <a name="syntax"></a>Syntax

`array_shift_left(`*`arr`*, *`shift_count`* `[,` *`fill_value`* ]`)`

## <a name="arguments"></a>Argumente

* *`arr`*: Das Eingabe Array, das geteilt werden soll, muss ein dynamisches Array sein.
* *`shift_count`*: Ganze Zahl, die die Anzahl der Positionen angibt, die Array Elemente nach links verschoben werden. Wenn der Wert negativ ist, werden die Elemente nach rechts verschoben.
* *`fill_value`*: Skalarwert, der zum Einfügen von Elementen anstelle der verschobenen und entfernten Elemente verwendet wird. Standard: NULL-Wert oder eine leere Zeichenfolge (abhängig vom *`arr`* Typ).

## <a name="returns"></a>Rückgabe

Dynamisches Array, das die gleiche Anzahl von Elementen wie im ursprünglichen Array enthält. Jedes Element wurde nach *shift_count*verschoben. Neue Elemente, die anstelle von entfernten Elementen hinzugefügt werden, haben den Wert *fill_value*.

## <a name="see-also"></a>Weitere Informationen

* Informationen zum Verschieben des Arrays right finden Sie unter [array_shift_right ()](array_shift_rightfunction.md).
* Informationen zum Drehen des Arrays right finden Sie unter [array_rotate_right ()](array_rotate_rightfunction.md).
* Informationen zum Drehen des Arrays Links finden Sie unter [array_rotate_left ()](array_rotate_leftfunction.md).

## <a name="examples"></a>Beispiele

* Verschieben nach links um zwei Positionen:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5, NULL, NULL]|

* Verschieben von zwei Positionen nach links und Hinzufügen des Standardwerts:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2, -1)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5,-1,-1]|


* Verschieben nach rechts um zwei Positionen mit negativem *shift_count* Wert:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, -2, -1)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1, 2, 3, 4, 5]|[-1,-1, 1, 2, 3]|
