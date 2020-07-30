---
title: isascii ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird isascii () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d8a060e4a332988fd966e0dec9ed07b3c76d0e3f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347288"
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