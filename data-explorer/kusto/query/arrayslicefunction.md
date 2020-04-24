---
title: array_slice() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird array_slice() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: ed5c320ef639f7545e19bcaabd9b53f4424c959d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518580"
---
# <a name="array_slice"></a>array_slice()

Extrahiert ein Segment eines dynamischen Arrays.

**Syntax**

`array_slice(`*arr*, *start*, *ende*]`)`

**Argumente**

* *arr*: Eingabe-Array, aus dem das Slice extrahiert werden soll, muss ein dynamisches Array sein.
* *start*: Nullbasierter (inklusive) Startindex des Slices, negative Werte werden in array_length+Start konvertiert.
* *End*: Nullbasierter (inklusiver) Endindex des Slices, negative Werte werden in array_length+Ende konvertiert.

Hinweis: Indizes a-bounds werden ignoriert.

**Rückgabe**

Dynamisches Array der Werte im Bereich [start.. Ende] von arr.

**Beispiele**

1.
```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|Arr|Geschnitten|
|---|---|
|[1,2,3]|[2,3]|


2.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|Arr|Geschnitten|
|---|---|
|[1,2,3,4,5]|[3,4,5]|


3.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|Arr|Geschnitten|
|---|---|
|[1,2,3,4,5]|[3,4]|