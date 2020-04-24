---
title: isnan() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird isnan() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 123d9cd32d645bb1225983138973a17b6bb9ecf3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513565"
---
# <a name="isnan"></a>isnan()

Gibt zurück, ob die Eingabe Not-a-Number (NaN)-Wert ist.  

**Syntax**

`isnan(`*X*`)`

**Argumente**

* *x*: Eine reelle Zahl.

**Rückgabe**

Ein Wert ungleich Null (true), wenn x NaN ist; und Null (falsch) sonst.

**Siehe auch**

* Informationen zum Überprüfen, ob der Wert null ist, finden Sie unter [isnull()](isnullfunction.md).
* Informationen zur Überprüfung, ob der Wert endlich ist, finden Sie unter [isfinite()](isfinitefunction.md).
* Informationen zur Überprüfung, ob der Wert unendlich ist, finden Sie unter [isinf()](isinffunction.md).

**Beispiel**

```kusto
range x from -1 to 1 step 1
| extend y = (-1*x) 
| extend div = 1.0*x/y
| extend isnan=isnan(div)
```

|x|y|div|Isnan|
|---|---|---|---|
|-1|1|-1|0|
|0|0|NaN|1|
|1|-1|-1|0|