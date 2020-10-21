---
title: IsEmpty ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird IsEmpty () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2944e90f14f38e2f136f5815a95584edc50d8235
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251733"
---
# <a name="isempty"></a>isempty()

Gibt zurück, `true` Wenn das Argument eine leere Zeichenfolge ist oder NULL ist.
    
```kusto
isempty("") == true
```

## <a name="syntax"></a>Syntax

`isempty(`[*Wert*]`)`

## <a name="returns"></a>Rückgabe

Gibt an, ob das Argument eine leere Zeichenfolge oder isnull ist.

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|"Parser" (" {} ")|false

## <a name="example"></a>Beispiel

```kusto
T
| where isempty(fieldName)
| count
```