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
ms.openlocfilehash: d1bea6260ca86e6ca47be843a6acc4fb43a037b3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347169"
---
# <a name="isnull"></a>isnull()

Wertet das einzige Argument aus und gibt einen `bool` Wert zurück, der angibt, ob das Argument zu einem NULL-Wert ausgewertet wird.

```kusto
isnull(parse_json("")) == true
```

## <a name="syntax"></a>Syntax

`isnull(`*Expr*`)`

## <a name="returns"></a>Rückgabe

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

## <a name="example"></a>Beispiel

```kusto
T | where isnull(PossiblyNull) | count
```