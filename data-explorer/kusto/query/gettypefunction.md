---
title: GetType ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird GetType () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 0efa07b7a1b050fe81ce2f369e8df5af4c05e212
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347662"
---
# <a name="gettype"></a>gettype()

Gibt den Lauf Zeittyp des einzelnen Arguments zurück.

Der Lauf Zeittyp unterscheidet sich möglicherweise vom nominalen (statischen) Typ für Ausdrücke, deren nominaler Typ ist `dynamic` . in solchen Fällen `gettype()` kann es hilfreich sein, den Typ des tatsächlichen Werts offenzulegen (wie der Wert im Arbeitsspeicher codiert ist).

## <a name="syntax"></a>Syntax

`gettype(`*Expr*`)`

## <a name="returns"></a>Rückgabe

Eine Zeichenfolge, die den Lauf Zeittyp des einzelnen Arguments darstellt.

## <a name="examples"></a>Beispiele

|Ausdruck                          |Rückgabe      |
|------------------------------------|-------------|
|`gettype("a")`                      |`string`     |
|`gettype(111)`                      |`long`       |
|`gettype(1==1)`                     |`bool`       |
|`gettype(now())`                    |`datetime`   |
|`gettype(1s)`                       |`timespan`   |
|`gettype(parse_json('1'))`           |`int`        |
|`gettype(parse_json(' "abc" '))`     |`string`     |
|`gettype(parse_json(' {"abc":1} '))` |`dictionary` | 
|`gettype(parse_json(' [1, 2, 3] '))` |`array`      |
|`gettype(123.45)`                   |`real`       |
|`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))`|`guid`| 
|`gettype(parse_json(''))`            |`null`|