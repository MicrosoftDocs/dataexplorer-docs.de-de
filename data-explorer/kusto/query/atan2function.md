---
title: atan2 ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt atan2 () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1229ff1476afe2863f07cfc0ff7aecffadd5867f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252763"
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