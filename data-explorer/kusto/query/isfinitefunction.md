---
title: isfinite() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird isfinite() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a17a39cce91fe039b2cf55cc5c98dba111cc334
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513599"
---
# <a name="isfinite"></a>isfinite()

Gibt zurück, ob die Eingabe ein endlicher Wert ist (ist weder unendlich noch NaN).

**Syntax**

`isfinite(`*X*`)`

**Argumente**

* *x*: Eine reelle Zahl.

**Rückgabe**

Ein Wert ungleich Null (true), wenn x endlich ist; und Null (falsch) sonst.

**Siehe auch**

* Informationen zum Überprüfen, ob der Wert null ist, finden Sie unter [isnull()](isnullfunction.md).
* Informationen zur Überprüfung, ob der Wert unendlich ist, finden Sie unter [isinf()](isinffunction.md).
* Informationen finden Sie unter [isnan()](isnanfunction.md).

**Beispiel**

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isfinite=isfinite(div)
```

|x|y|div|isfinit|
|---|---|---|---|
|-1|0|-∞|0|
|0|0|NaN|0|
|1|0|∞|0|