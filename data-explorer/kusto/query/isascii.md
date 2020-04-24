---
title: isascii() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird isascii() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: daba0f4015a4847155309964f8ac0909ff4bc9d0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513718"
---
# <a name="isascii"></a>isascii()

Gibt `true` zurück, wenn das Argument eine gültige ascii-Zeichenfolge ist.
    
```kusto
isascii("some string") == true
```

**Syntax**

`isascii(`[*Wert*]`)`

**Rückgabe**

Gibt an, ob es sich bei dem Argument um eine gültige ascii-Zeichenfolge handelt.

**Beispiel**

```kusto
T
| where isascii(fieldName)
| count
```