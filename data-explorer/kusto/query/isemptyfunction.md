---
title: IsEmpty ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird IsEmpty () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ac2bf5d5ea55172cbdb07bf90704ae5ad497e925
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347271"
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