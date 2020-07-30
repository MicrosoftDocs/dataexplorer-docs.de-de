---
title: isFinite ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird isFinite () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f53c74fd2ac56219351c4d194c93e9fa4b627cf4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347254"
---
# <a name="isfinite"></a>isfinite()

Gibt zurück, ob die Eingabe ein endlicher Wert ist (ist weder unendlich noch NaN).

## <a name="syntax"></a>Syntax

`isfinite(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl.

## <a name="returns"></a>Rückgabe

Ein Wert ungleich 0 (true), wenn x nicht begrenzt ist. und NULL (false) andernfalls.

**Weitere Informationen**

* Informationen zum Überprüfen, ob der Wert NULL ist, finden Sie unter [IsNull ()](isnullfunction.md).
* Informationen zum Überprüfen, ob der Wert unendlich ist, finden Sie unter [isinf ()](isinffunction.md).
* Informationen zum Überprüfen, ob der Wert NaN (not-a-Number) ist, finden Sie unter [IsNaN ()](isnanfunction.md).

## <a name="example"></a>Beispiel

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isfinite=isfinite(div)
```

|x|y|div|isFinite|
|---|---|---|---|
|-1|0|-∞|0|
|0|0|NaN|0|
|1|0|∞|0|