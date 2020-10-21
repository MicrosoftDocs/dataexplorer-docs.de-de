---
title: isnotempty ()-Azure Daten-Explorer
description: In diesem Artikel wird isnotempty () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 42699b1d4a6f225213b2a2d55520cb019a983121
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253225"
---
# <a name="isnotempty"></a>isnotempty()

Gibt zurück `true` , wenn das Argument keine leere Zeichenfolge ist und nicht NULL ist.

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>Syntax

`isnotempty(`[*Wert*]`)`

`notempty(`[*Wert*] `)` --Alias von `isnotempty`

## <a name="example"></a>Beispiel

```kusto
T
| where isnotempty(fieldName)
| count
```
