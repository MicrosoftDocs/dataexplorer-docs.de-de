---
title: isascii ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird isascii () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a17836597277b2cf5401f2caeaa60b44da731dd5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251772"
---
# <a name="isascii"></a>isascii()

Gibt zurück, `true` Wenn das-Argument eine gültige ASCII-Zeichenfolge ist.
    
```kusto
isascii("some string") == true
```

## <a name="syntax"></a>Syntax

`isascii(`[*Wert*]`)`

## <a name="returns"></a>Rückgabe

Gibt an, ob das Argument eine gültige ASCII-Zeichenfolge ist.

## <a name="example"></a>Beispiel

```kusto
T
| where isascii(fieldName)
| count
```