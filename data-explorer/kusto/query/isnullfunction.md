---
title: IsNull ()-Azure Daten-Explorer
description: In diesem Artikel wird IsNull () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4c57c7aba2bff2dfaecfa72b20ab76cc84ed17d6
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550587"
---
# <a name="isnull"></a>isnull()

Wertet das einzige Argument aus und gibt einen `bool` Wert zurück, der angibt, ob das Argument zu einem NULL-Wert ausgewertet wird.

```kusto
isnull(parse_json("")) == true
```

**Syntax**

`isnull(`*Expr*`)`

**Rückgabe**

True oder false, abhängig davon, ob der Wert NULL ist.

**Hinweise**

* `string`Werte dürfen nicht NULL sein. Verwenden Sie [IsEmpty](./isemptyfunction.md) , um zu bestimmen, ob ein Wert vom Typ `string` leer ist.

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