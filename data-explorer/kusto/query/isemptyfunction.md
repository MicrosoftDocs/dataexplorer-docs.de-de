---
title: isempty() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird isempty() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2cb0e53aa16257398c20661c31494ca9dda17c1e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513616"
---
# <a name="isempty"></a>isempty()

Gibt `true` zurück, wenn das Argument eine leere Zeichenfolge oder null ist.
    
```kusto
isempty("") == true
```

**Syntax**

`isempty(`[*Wert*]`)`

**Rückgabe**

Gibt an, ob das Argument eine leere Zeichenfolge oder isnull ist.

|x|isempty(x)
|---|---
| "" | true
|"x" | False
|parsejson("")|true
|parsejson("[]")|False
|parsejson("{}")|False

**Beispiel**

```kusto
T
| where isempty(fieldName)
| count
```