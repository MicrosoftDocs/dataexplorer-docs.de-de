---
title: isnotnull() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird isnotnull() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8be57f2f7484081ac316a8af6fea252a60f895a4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513446"
---
# <a name="isnotnull"></a>isnotnull()

Gibt `true` zurück, wenn das Argument nicht null ist.

**Syntax**

`isnotnull(`[*Wert*]`)`

`notnull(`[*Wert*] `)` - Alias für`isnotnull`

**Beispiel**

```kusto
T | where isnotnull(PossiblyNull) | count
```

Beachten Sie, dass es andere Möglichkeiten gibt, diesen Effekt zu erreichen:

```kusto
T | summarize count(PossiblyNull)
```