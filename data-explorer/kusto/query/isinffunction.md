---
title: isinf() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt isinf() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d93697890ee05cabf9ca1830ac047d90d8c9e844
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513582"
---
# <a name="isinf"></a>isinf()

Gibt zurück, ob die Eingabe ein unendlicher (positiver oder negativer) Wert ist.  

**Syntax**

`isinf(`*X*`)`

**Argumente**

* *x*: Eine reelle Zahl.

**Rückgabe**

Ein Wert ungleich Null (true), wenn x eine positive oder negative Unendlichkeit ist; und Null (falsch) sonst.

**Siehe auch**

* Informationen zum Überprüfen, ob der Wert null ist, finden Sie unter [isnull()](isnullfunction.md).
* Informationen zur Überprüfung, ob der Wert endlich ist, finden Sie unter [isfinite()](isfinitefunction.md).
* Informationen finden Sie unter [isnan()](isnanfunction.md).

**Beispiel**

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
