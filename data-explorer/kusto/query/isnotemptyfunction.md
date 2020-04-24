---
title: isnotempty() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird isnotempty() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 14111be0fc0247dd151ef7454121e6b90a32ff0d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513531"
---
# <a name="isnotempty"></a>isnotempty()

Gibt `true` zurück, wenn das Argument keine leere Zeichenfolge oder ein NULL-Zeichen ist.

```kusto
isnotempty("") == false
```

**Syntax**

`isnotempty(`[*Wert*]`)`

`notempty(`[*Wert*] `)` -- Alias von`isnotempty`