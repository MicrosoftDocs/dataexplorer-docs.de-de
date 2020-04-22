---
title: prev() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird prev() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33f6045333826b21ddc0092e2cc7d5e033c12a96
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744596"
---
# <a name="prev"></a>prev()

Gibt den Wert einer Spalte in einer Zeile zurück, die bei einem Versatz vor der aktuellen Zeile in einem [serialisierten Zeilensatz](./windowsfunctions.md#serialized-row-set).

**Syntax**

`prev(column)`

`prev(column, offset)`

`prev(column, offset, default_value)`

**Argumente**

* `column`: die Spalte, aus der die Werte abstammen sollen.

* `offset`: der Offset, um in Zeilen zurückzulaufen. Wenn kein Offset angegeben ist, wird ein Standardoffset 1 verwendet.

* `default_value`: Der Standardwert, der verwendet werden soll, wenn keine vorherigen Zeilen vorhanden sind, aus denen der Wert entnommen werden kann. Wenn kein Standardwert angegeben ist, wird null verwendet.


**Beispiele**

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```