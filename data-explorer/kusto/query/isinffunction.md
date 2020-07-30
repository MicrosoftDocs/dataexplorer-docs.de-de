---
title: isinf ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird isinf () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 71a37d7a1bd700b5f929c009197a382315099e08
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347237"
---
# <a name="isinf"></a>isinf()

Gibt zurück, ob die Eingabe ein unendlicher (positiver oder negativer) Wert ist.  

## <a name="syntax"></a>Syntax

`isinf(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl.

## <a name="returns"></a>Rückgabe

Ein Wert ungleich 0 (true), wenn x ein positives oder negatives unendliches Ergebnis ist. und NULL (false) andernfalls.

**Weitere Informationen**

* Informationen zum Überprüfen, ob der Wert NULL ist, finden Sie unter [IsNull ()](isnullfunction.md).
* Informationen zum Überprüfen, ob der Wert begrenzt ist, finden Sie unter [isFinite ()](isfinitefunction.md).
* Informationen zum Überprüfen, ob der Wert NaN (not-a-Number) ist, finden Sie unter [IsNaN ()](isnanfunction.md).

## <a name="example"></a>Beispiel

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isinf=isinf(div)
```

|x|y|div|isinf|
|---|---|---|---|
|-1|0|-∞|1|
|0|0|NaN|0|
|1|0|∞|1|
