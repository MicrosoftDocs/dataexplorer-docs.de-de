---
title: Prev ()-Azure Daten-Explorer
description: In diesem Artikel wird Prev () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0ef9fe5160d433554880ac1be0c4a3409d286f17
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249609"
---
# <a name="prev"></a>prev()

Gibt den Wert einer bestimmten Spalte in einer angegebenen Zeile zurück.
Die angegebene Zeile befindet sich an einem angegebenen Offset aus der aktuellen Zeile in einem [serialisierten Zeilen Satz](./windowsfunctions.md#serialized-row-set).

## <a name="syntax"></a>Syntax

Es gibt mehrere Möglichkeiten.

* `prev(column)`

* `prev(column, offset)`

* `prev(column, offset, default_value)`

## <a name="arguments"></a>Argumente

* `column`: Die Spalte, aus der die Werte abgeleitet werden sollen.

* `offset`: Der Offset, der in Zeilen zurückgehen soll. Wenn kein Offset angegeben wird, wird ein Standard Offset 1 verwendet.

* `default_value`: Der Standardwert, der verwendet werden soll, wenn keine vorherigen Zeilen vorhanden sind, aus denen der Wert genommen werden soll. Wenn kein Standardwert angegeben ist, wird NULL verwendet.

## <a name="examples"></a>Beispiele

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```
