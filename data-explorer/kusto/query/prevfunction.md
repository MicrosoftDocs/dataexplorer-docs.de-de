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
ms.openlocfilehash: 4216f691345c7dffd3bb1974e5f82e877ffb70f2
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128987"
---
# <a name="prev"></a>prev()

Gibt den Wert einer bestimmten Spalte in einer angegebenen Zeile zurück.
Die angegebene Zeile befindet sich an einem angegebenen Offset aus der aktuellen Zeile in einem [serialisierten Zeilen Satz](./windowsfunctions.md#serialized-row-set).

**Syntax**

Es gibt mehrere Möglichkeiten.

* `prev(column)`

* `prev(column, offset)`

* `prev(column, offset, default_value)`

**Argumente**

* `column`: Die Spalte, aus der die Werte abgeleitet werden sollen.

* `offset`: Der Offset, der in Zeilen zurückgehen soll. Wenn kein Offset angegeben wird, wird ein Standard Offset 1 verwendet.

* `default_value`: Der Standardwert, der verwendet werden soll, wenn keine vorherigen Zeilen vorhanden sind, aus denen der Wert genommen werden soll. Wenn kein Standardwert angegeben ist, wird NULL verwendet.

**Beispiele**

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```
