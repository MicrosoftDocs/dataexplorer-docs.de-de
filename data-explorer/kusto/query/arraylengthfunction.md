---
title: array_length() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird array_length() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0bb1daeba6d24f8bd7326fcd0b8c17f06003e30b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518597"
---
# <a name="array_length"></a>array_length()

Berechnet die Anzahl der Elemente in einem dynamischen Array.

**Syntax**

`array_length(`*array*`)`

**Argumente**

* *Array*: `dynamic` Ein Wert.

**Rückgabe**

Die Anzahl von Elementen in *array* oder `null`, wenn *array* kein Array ist.

**Beispiele**

```kusto
print array_length(parse_json('[1, 2, 3, "four"]')) == 4

print array_length(parse_json('[8]')) == 1

print array_length(parse_json('[{}]')) == 1

print array_length(parse_json('[]')) == 0

print array_length(parse_json('{}')) == null

print array_length(parse_json('21')) == null
```