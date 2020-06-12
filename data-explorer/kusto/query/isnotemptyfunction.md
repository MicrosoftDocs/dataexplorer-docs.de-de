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
ms.openlocfilehash: 5a21031b07df3a4fa654fd13eb3761618308337b
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717341"
---
# <a name="isnotempty"></a>isnotempty()

Gibt zurück `true` , wenn das Argument keine leere Zeichenfolge ist und nicht NULL ist.

```kusto
isnotempty("") == false
```

**Syntax**

`isnotempty(`[*Wert*]`)`

`notempty(`[*Wert*] `)` --Alias von`isnotempty`
