---
title: Next ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die nächste ()-Daten-Explorer in Azure beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ca9361c0a43a2881f7448312e4f8a5129426e55a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248733"
---
# <a name="next"></a>next()

Gibt den Wert einer Spalte in einer Zeile zurück, die an einem Offset nach der aktuellen Zeile in einem [serialisierten Zeilen Satz liegt](./windowsfunctions.md#serialized-row-set).

## <a name="syntax"></a>Syntax

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

## <a name="arguments"></a>Argumente

* `column`: die Spalte, aus der die Werte abgeleitet werden sollen.

* `offset`: der Offset, der in Zeilen fort geht. Wenn kein Offset angegeben wird, wird ein Standard Offset 1 verwendet.

* `default_value`: der Standardwert, der verwendet werden soll, wenn keine nächsten Zeilen vorhanden sind, aus denen der Wert genommen werden soll. Wenn kein Standardwert angegeben ist, wird NULL verwendet.


## <a name="examples"></a>Beispiele
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```