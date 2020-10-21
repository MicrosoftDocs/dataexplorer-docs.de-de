---
title: array_slice ()-Azure Daten-Explorer
description: In diesem Artikel wird array_slice () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: ecb30d00a6c95e3754686eb264d9439c1c1e605f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246754"
---
# <a name="array_slice"></a>array_slice()

Extrahiert einen Slice eines dynamischen Arrays.

## <a name="syntax"></a>Syntax

`array_slice`(*`arr`*, *`start`*, *`end`*)

## <a name="arguments"></a>Argumente

* *`arr`*: Das Eingabe Array, aus dem der Slice extrahiert werden soll, muss ein dynamisches Array sein.
* *`start`*: NULL basierter (inklusiver) Start Index des Slice, negative Werte werden in array_length + Start konvertiert.
* *`end`*: NULL basierter (inklusiver) Endindex des Slice, negative Werte werden in array_length + Ende konvertiert.

Hinweis: Out-of-Bounds-Indizes werden ignoriert.

## <a name="returns"></a>Rückgabe

Dynamisches Array der Werte im Bereich [ `start..end` ] von `arr` .

## <a name="examples"></a>Beispiele

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
