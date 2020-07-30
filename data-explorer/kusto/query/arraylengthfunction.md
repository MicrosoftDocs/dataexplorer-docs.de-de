---
title: array_length ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird array_length () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 14203e3078b7fe30222ea26320ed1391000d5c05
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349549"
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