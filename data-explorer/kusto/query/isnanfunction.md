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
ms.openlocfilehash: 5597f21d5e426329e2793978a6b207efc3868d13
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347220"
---
# <a name="isnan"></a>isnan()

Gibt zurück, ob die Eingabe ein NaN-Wert (not-a-Number) ist.  

## <a name="syntax"></a>Syntax

`isnan(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl.

## <a name="returns"></a>Rückgabe

Ein Wert ungleich 0 (true), wenn x NaN ist. und NULL (false) andernfalls.

**Weitere Informationen**

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