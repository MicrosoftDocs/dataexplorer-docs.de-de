---
title: array_shift_left ()-Azure Daten-Explorer
description: In diesem Artikel wird array_shift_left () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 883aa2e22c02584de34c705b89a979b86e10475e
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102787"
---
# <a name="array_shift_left"></a>array_shift_left()

`array_shift_left()` Verschiebt die Werte in einem Array nach links.

## <a name="syntax"></a>Syntax

`array_shift_left(`*`arr`*, *`shift_count`* `[,` *`fill_value`* ]`)`

## <a name="arguments"></a>Argumente

* *`arr`*: Das Eingabe Array, das geteilt werden soll, muss ein dynamisches Array sein.
* *`shift_count`*: Ganze Zahl, die die Anzahl der Positionen angibt, die Array Elemente nach links verschoben werden. Wenn der Wert negativ ist, werden die Elemente nach rechts verschoben.
* *`fill_value`*: Skalarwert, der zum Einfügen von Elementen anstelle der verschobenen und entfernten Elemente verwendet wird. Standard: NULL-Wert oder eine leere Zeichenfolge (abhängig vom *`arr`* Typ).

## <a name="returns"></a>Gibt zurück

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
