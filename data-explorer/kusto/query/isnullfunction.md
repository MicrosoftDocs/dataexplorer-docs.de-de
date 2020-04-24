---
title: isnull() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt isnull() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e26dca661ceac1ad209358b24b3f8d497a5c3049
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513412"
---
# <a name="isnull"></a>isnull()

Bewertet das einzige Argument `bool` und gibt einen Wert zurück, der angibt, ob das Argument zu einem NULL-Wert ausgewertet wird.

```kusto
isnull(parse_json("")) == true
```

**Syntax**

`isnull(`*Expr*`)`

**Rückgabe**

True oder False, je nachdem, ob ist der Wert null oder nicht null ist.

**Hinweise**

* `string`Werte können nicht NULL sein. Verwenden Sie [isempty,](./isemptyfunction.md) um `string` zu bestimmen, ob ein Wert des Typs leer ist oder nicht.

|x                |`isnull(x)`|
|-----------------|-----------|
|`""`             |`false`    |
|`"x"`            |`false`    |
|`parse_json("")`  |`true`     |
|`parse_json("[]")`|`false`    |
|`parse_json("{}")`|`false`    |

**Beispiel**

```kusto
T | where isnull(PossiblyNull) | count
```