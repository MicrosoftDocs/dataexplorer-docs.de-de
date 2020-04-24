---
title: decke() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird ceiling() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f2ecd043c43bb1af6530364d200d5dc9db640f95
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517254"
---
# <a name="ceiling"></a>ceiling()

Berechnet die kleinste ganze Zahl größer oder gleich dem angegebenen numerischen Ausdruck.

**Syntax**

`ceiling(`*X*`)`

**Argumente**

* *x*: Eine reelle Zahl.

**Rückgabe**

* Die kleinste ganze Zahl, die größer oder gleich dem angegebenen numerischen Ausdruck ist. 

**Beispiele**

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|c2|c3|
|---|---|---|
|-1|0|1|