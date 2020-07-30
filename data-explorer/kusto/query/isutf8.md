---
title: isutf8 ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt isutf8 () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 952ea030d351a9e23fe26bbd7f27a96d182a89e3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347152"
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