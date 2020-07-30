---
title: isnotempty ()-Azure Daten-Explorer
description: In diesem Artikel wird isnotempty () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d325861c7264c77535caf6df6c363ec801dd697
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347203"
---
# <a name="isnotempty"></a>isnotempty()

Gibt zurück `true` , wenn das Argument keine leere Zeichenfolge ist und nicht NULL ist.

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>Syntax

`isnotempty(`[*Wert*]`)`

`notempty(`[*Wert*] `)` --Alias von`isnotempty`
