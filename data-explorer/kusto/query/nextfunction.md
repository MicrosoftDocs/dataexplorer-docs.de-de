---
title: next() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird next() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f45e88942fdf9eb23451e1391866f57ca5f0e21a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512120"
---
# <a name="next"></a>next()

Gibt den Wert einer Spalte in einer Zeile zurück, die nach der aktuellen Zeile in einem [serialisierten Zeilensatz](./windowsfunctions.md#serialized-row-set)bei einem Versatz versetzt wird.

**Syntax**

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

**Argumente**

* `column`: die Spalte, aus der die Werte abstammen sollen.

* `offset`: der Offset, um in Zeilen voranzukommen. Wenn kein Offset angegeben ist, wird ein Standardoffset 1 verwendet.

* `default_value`: Der Standardwert, der verwendet werden soll, wenn keine nächsten Zeilen vorhanden sind, aus denen der Wert entnommen werden kann. Wenn kein Standardwert angegeben ist, wird null verwendet.


**Beispiele**
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```