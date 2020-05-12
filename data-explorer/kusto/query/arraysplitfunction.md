---
title: array_split ()-Azure Daten-Explorer
description: In diesem Artikel wird array_split () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: 102077c9c1116bd9476c6dae59d993a6379b69bd
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225551"
---
# <a name="array_split"></a>array_split()

Teilt ein Array entsprechend den getrennten Indizes in mehrere Arrays und packt das generierte Array in ein dynamisches Array.

**Syntax**

`array_split`(*`arr`*, *`indices`*)

**Argumente**

* *`arr`*: Das Eingabe Array, das geteilt werden soll, muss ein dynamisches Array sein.
* *`indices`*: Integer oder dynamisches Array von Ganzzahlen mit den getrennten Indizes (null basiert) werden negative Werte in array_length +-Wert konvertiert.

**Rückgabe**

Dynamisches Array mit n + 1 Arrays mit den Werten im Bereich `[0..i1), [i1..i2), ... [iN..array_length)` von `arr` , wobei N die Anzahl der Eingabe Indizes und `i1...iN` die Indizes ist.

**Beispiele**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```

|`arr`|`arr_split`|
|---|---|
|[1, 2, 3, 4, 5]|[[1, 2], [3, 4, 5]]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```

|`arr`|`arr_split`|
|---|---|
|[1, 2, 3, 4, 5]|[[1], [2, 3], [4, 5]]|
