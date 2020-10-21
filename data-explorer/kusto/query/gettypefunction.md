---
title: GetType ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird GetType () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8fc1c3949ef13e504de6ba76be1bd5e600926288
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244799"
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