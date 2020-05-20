---
title: array_shift_right ()-Azure Daten-Explorer
description: In diesem Artikel wird array_shift_right () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 28a44365d6d79bf30ec188146d989f2af2ad12c1
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550655"
---
# <a name="array_shift_right"></a>array_shift_right()

`array_shift_right()`verschiebt Werte in einem Array nach rechts.

**Syntax**

`array_shift_right(`*`arr`*, *`shift_count`* [, *`fill_value`* ]`)`

**Argumente**

* *`arr`*: Das Eingabe Array, das geteilt werden soll, muss ein dynamisches Array sein.
* *`shift_count`*: Ganze Zahl, die die Anzahl der Positionen angibt, die Array Elemente nach rechts verschoben werden. Wenn der Wert negativ ist, werden die Elemente nach links verschoben.
* *`fill_value`*: Skalarwert, der zum Einfügen von Elementen anstelle der verschobenen und entfernten Elemente verwendet wird. Standard: NULL-Wert oder eine leere Zeichenfolge (abhängig vom *arr* -Typ).

**Rückgabe**

Dynamisches Array, das die gleiche Menge an Elementen wie im ursprünglichen Array enthält. Jedes Element wurde nach verschoben *`shift_count`* . Neue Elemente, die anstelle der entfernten Elemente hinzugefügt werden, haben den Wert *`fill_value`* .

**Siehe auch**

* Informationen zum Verschieben des Arrays Links finden Sie unter [array_shift_left ()](array_shift_leftfunction.md).
* Informationen zum Drehen des Arrays right finden Sie unter [array_rotate_right ()](array_rotate_rightfunction.md).
* Informationen zum Drehen des Arrays Links finden Sie unter [array_rotate_left ()](array_rotate_leftfunction.md).

**Beispiele**

* Verschieben nach rechts um zwei Positionen:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2)
    ```
    
    |r|arr_shift|
    |---|---|
    |[1, 2, 3, 4, 5]|[NULL, NULL, 1, 2, 3]|

* Verschieben von zwei Positionen nach rechts und Hinzufügen eines Standardwerts:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2, -1)
    ```
    
    |r|arr_shift|
    |---|---|
    |[1, 2, 3, 4, 5]|[-1,-1, 1, 2, 3]|

* Verschieben nach links um zwei Positionen mithilfe eines negativen shift_count Werts:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, -2, -1)
    ```
    
    |r|arr_shift|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5,-1,-1]|
    