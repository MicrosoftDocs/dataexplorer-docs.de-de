---
title: isutf8 ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt isutf8 () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c9ad35964eb6d5a8c4a38b5ecdeb1eae43221d52
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247340"
---
# <a name="isutf8"></a>isutf8()

Gibt zurück, `true` Wenn das Argument eine gültige UTF8-Zeichenfolge ist.
    
```kusto
isutf8("some string") == true
```

## <a name="syntax"></a>Syntax

`isutf8(`[*Wert*]`)`

## <a name="returns"></a>Rückgabe

Gibt an, ob das Argument eine gültige UTF8-Zeichenfolge ist.

## <a name="example"></a>Beispiel

```kusto
T
| where isutf8(fieldName)
| count
```