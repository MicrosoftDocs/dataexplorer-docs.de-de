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
ms.openlocfilehash: 84aa0d815328532f7a627958b56c41b998665d6b
ms.sourcegitcommit: f7bebd245081a5cdc08e88fa4f9a769c18e13e5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94644602"
---
# <a name="gettype"></a>gettype()

Gibt den Lauf Zeittyp des einzelnen Arguments zurück.

Der Lauf Zeittyp unterscheidet sich möglicherweise vom nominalen (statischen) Typ für Ausdrücke, deren nominaler Typ ist `dynamic` . in solchen Fällen `gettype()` kann es hilfreich sein, den Typ des tatsächlichen Werts offenzulegen (wie der Wert im Arbeitsspeicher codiert ist).

## <a name="syntax"></a>Syntax

`gettype(`*Expr*`)`

## <a name="returns"></a>Gibt zurück

Eine Zeichenfolge, die den Lauf Zeittyp des einzelnen Arguments darstellt.

## <a name="examples"></a>Beispiele

|Ausdruck                          |Gibt zurück      |
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
