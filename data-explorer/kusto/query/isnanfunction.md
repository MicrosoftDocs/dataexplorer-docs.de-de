---
title: IsNaN ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird IsNaN () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f73effae8d91524f46548d57288a23d79cffd0a5
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103267"
---
# <a name="isnan"></a>isnan()

Gibt zurück, ob die Eingabe ein NaN-Wert (not-a-Number) ist.  

## <a name="syntax"></a>Syntax

`isnan(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl.

## <a name="returns"></a>Gibt zurück

Ein Wert ungleich 0 (true), wenn x NaN ist. und NULL (false) andernfalls.

## <a name="see-also"></a>Weitere Informationen

* Informationen zum Überprüfen, ob der Wert NULL ist, finden Sie unter [IsNull ()](isnullfunction.md).
* Informationen zum Überprüfen, ob der Wert begrenzt ist, finden Sie unter [isFinite ()](isfinitefunction.md).
* Informationen zum Überprüfen, ob der Wert unendlich ist, finden Sie unter [isinf ()](isinffunction.md).

## <a name="example"></a>Beispiel

```kusto
range x from -1 to 1 step 1
| extend y = (-1*x) 
| extend div = 1.0*x/y
| extend isnan=isnan(div)
```

|x|y|div|IsNaN|
|---|---|---|---|
|-1|1|-1|0|
|0|0|NaN|1|
|1|-1|-1|0|