---
title: array_length ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird array_length () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3777194270e91d75d68c3af544e18b62358f709e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246772"
---
# <a name="array_length"></a>array_length()

Berechnet die Anzahl der Elemente in einem dynamischen Array.

## <a name="syntax"></a>Syntax

`array_length(`*array*`)`

## <a name="arguments"></a>Argumente

* *Array*: ein- `dynamic` Wert.

## <a name="returns"></a>Rückgabe

Die Anzahl von Elementen in *array* oder `null`, wenn *array* kein Array ist.

## <a name="examples"></a>Beispiele

```kusto
print array_length(parse_json('[1, 2, 3, "four"]')) == 4

print array_length(parse_json('[8]')) == 1

print array_length(parse_json('[{}]')) == 1

print array_length(parse_json('[]')) == 0

print array_length(parse_json('{}')) == null

print array_length(parse_json('21')) == null
```