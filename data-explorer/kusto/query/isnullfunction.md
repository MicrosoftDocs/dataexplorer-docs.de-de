---
title: IsNull ()-Azure Daten-Explorer
description: In diesem Artikel wird IsNull () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: afaff2c00ca9136e113639deed886d039d21fda9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241524"
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

**Notizen**

* `string` Werte dürfen nicht NULL sein. Verwenden Sie [IsEmpty](./isemptyfunction.md) , um zu bestimmen, ob ein Wert vom Typ `string` leer ist.

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