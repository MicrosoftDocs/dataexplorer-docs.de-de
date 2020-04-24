---
title: array_split() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird array_split() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: 4d5d8ce5e918f335f1f26f4e3fc045ab0fa6e3a1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518563"
---
# <a name="array_split"></a>array_split()

Teilt ein Array entsprechend den Geteilteindizes in mehrere Arrays auf und packt das generierte Array in einem dynamischen Array.

**Syntax**

`array_split(`*arr*, *Indizes*`)`

**Argumente**

* *arr*: Eingabe-Array zu teilen, muss dynamisches Array sein.
* *Indizes*: Ganzzahl- oder dynamisches Array von Ganzzahlen mit den geteilten Indizes (nullbasiert), negative Werte werden in array_length + Wert konvertiert.

**Rückgabe**

Dynamisches Array, das N+1-Arrays mit den Werten im Bereich [0..i1), [i1.. i2), ... [iN.. array_length) von arr, wobei N die Anzahl der Eingabeindizes und i1.. iN sind die Indizes.

**Beispiele**

1.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```
|Arr|arr_split|
|---|---|
|[1,2,3,4,5]|[[1,2],[3,4,5]]|



2.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```
|Arr|arr_split|
|---|---|
|[1,2,3,4,5]|[[1],[2,3],[4,5]]|