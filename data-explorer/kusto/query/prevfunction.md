---
title: Prev ()-Azure Daten-Explorer
description: In diesem Artikel wird Prev () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb781834d77aee678103a14714721ff0d46f7b3a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346098"
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
