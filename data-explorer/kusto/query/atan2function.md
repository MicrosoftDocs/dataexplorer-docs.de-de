---
title: atan2 ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt atan2 () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 60b500109f140290427a6d1ad3baba8e25849b57
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349447"
---
# <a name="atan2"></a>atan2()

Berechnet den Winkel im Bogenmaße zwischen der positiven x-Achse und dem Strahl vom Ursprung bis zum Punkt (y, x).

## <a name="syntax"></a>Syntax

`atan2(`*j* `,` *x*`)`

## <a name="arguments"></a>Argumente

* *x*: x-Koordinate (eine reelle Zahl).
* *y*: y-Koordinate (eine reelle Zahl).

## <a name="returns"></a>Rückgabe

* Der Winkel im Bogenmaße zwischen der positiven x-Achse und dem Strahl vom Ursprung bis zum Punkt (y, x).

## <a name="examples"></a>Beispiele

```kusto
print atan2_0 = atan2(1,1) // Pi / 4 radians (45 degrees)
| extend atan2_1 = atan2(0,-1) // Pi radians (180 degrees)
| extend atan2_2 = atan2(-1,0) // - Pi / 2 radians (-90 degrees)
```

|atan2_0|atan2_1|atan2_2|
|---|---|---|
|0.785398163397448|3,14159265358979|-1,5707963267949|