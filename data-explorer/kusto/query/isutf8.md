---
title: isutf8() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird isutf8() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 619fb90b72fed8ec0e10fe05ddc3c6df6ff1386e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513395"
---
# <a name="isutf8"></a>isutf8()

Gibt `true` zurück, wenn das Argument eine gültige utf8-Zeichenfolge ist.
    
```kusto
isutf8("some string") == true
```

**Syntax**

`isutf8(`[*Wert*]`)`

**Rückgabe**

Gibt an, ob es sich bei dem Argument um eine gültige utf8-Zeichenfolge handelt.

**Beispiel**

```kusto
T
| where isutf8(fieldName)
| count
```