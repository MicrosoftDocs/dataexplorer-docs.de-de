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
ms.openlocfilehash: 9f7811c2668bd0bb2d6e3711800ee5d9a450b9ec
ms.sourcegitcommit: 487485c87706183a9824f695e5b56b0bc5ade048
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89056233"
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
