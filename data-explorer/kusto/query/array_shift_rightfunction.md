---
title: array_shift_right() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird array_shift_right() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 73f87d4c5ce1a841404e438e0e5647089b38785f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518767"
---
# <a name="array_shift_right"></a>array_shift_right()

`array_shift_right()`verschiebt Werte innerhalb eines Arrays nach rechts.

**Syntax**

`array_shift_right(`*arr*, *shift_count* [, *fill_value* ]`)`

**Argumente**

* *arr*: Eingabe-Array zu teilen, muss dynamisches Array sein.
* *shift_count*: Ganzzahl, die die Anzahl der Positionen angibt, die Arrayelemente nach rechts verschoben werden. Wenn der Wert negativ ist, werden die Elemente nach links verschoben.
* *fill_value*: Skalarwert, der zum Einfügen von Elementen anstelle der Elemente verwendet wird, die verschoben und entfernt wurden. Standardwert: NULL-Wert oder leere *arr* Zeichenfolge (abhängig vom arr-Typ).

**Rückgabe**

Dynamisches Array, das die gleiche Anzahl der Elemente enthält wie im ursprünglichen Array, wobei jedes Element gemäß *shift_count*verschoben wurde. Neue Elemente, die anstelle der entfernten Elemente hinzugefügt werden, haben den Wert *fill_value*.

**Siehe auch**

* Informationen zum Verschieben des Arrays nach links finden [Sie unter array_shift_left()](array_shift_leftfunction.md).
* Informationen zum Drehen des Arrays rechts finden [Sie unter array_rotate_right()](array_rotate_rightfunction.md).
* Informationen zum Drehen des Arrays links finden [Sie unter array_rotate_left()](array_rotate_leftfunction.md).

**Beispiele**

* Umschalten nach rechts um zwei Positionen:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[null,null,1,2,3]|

* Verschieben nach rechts um zwei Positionen und Hinzufügen eines Standardwerts:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2, -1)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[-1,-1,1,2,3]|


* Verschieben nach links um zwei Positionen mit einem negativen shift_count Wert:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, -2, -1)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,-1,-1]|