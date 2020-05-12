---
title: array_slice ()-Azure Daten-Explorer
description: In diesem Artikel wird array_slice () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: 98ed69475232dde474f21d1f4a7f792c82a69637
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225614"
---
# <a name="array_slice"></a>array_slice()

Extrahiert einen Slice eines dynamischen Arrays.

**Syntax**

`array_slice`(*`arr`*, *`start`*, *`end`*])

**Argumente**

* *`arr`*: Das Eingabe Array, aus dem der Slice extrahiert werden soll, muss ein dynamisches Array sein.
* *`start`*: NULL basierter (inklusiver) Start Index des Slice, negative Werte werden in array_length + Start konvertiert.
* *`end`*: NULL basierter (inklusiver) Endindex des Slice, negative Werte werden in array_length + Ende konvertiert.

Hinweis: Out-of-Bounds-Indizes werden ignoriert.

**Rückgabe**

Dynamisches Array der Werte im Bereich [ `start..end` ] von `arr` .

**Beispiele**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|`arr`|`sliced`|
|---|---|
|[1, 2, 3]|[2, 3]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|`arr`|Salami|
|---|---|
|[1, 2, 3, 4, 5]|[3, 4, 5]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|`arr`|Salami|
|---|---|
|[1, 2, 3, 4, 5]|[3, 4]|
