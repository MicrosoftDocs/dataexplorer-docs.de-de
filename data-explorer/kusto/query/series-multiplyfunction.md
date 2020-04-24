---
title: series_multiply() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_multiply() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: fa000d1058730e0232790e7f0e3976fa203519c0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508210"
---
# <a name="series_multiply"></a>series_multiply()

Berechnet die elementbezogene Multiplikation von zwei numerischen Reiheneingaben.

**Syntax**

`series_multiply(`*Serie1* `,` *Serie2*`)`

**Argumente**

* *series1, series2*: Geben Sie numerische Arrays ein, um elementweise in ein dynamisches Arrayergebnis multipliziert zu werden. Alle Argumente müssen dynamische Arrays sein. 

**Rückgabe**

Dynamisches Array der berechneten elementweisen Multiplikationsoperation zwischen den beiden Eingängen. Jedes nicht numerische Element oder nicht vorhandene Element (Arrays `null` unterschiedlicher Größe) ergibt einen Elementwert.

**Beispiel**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_multiply_s2 = series_multiply(s1, s2)
```

|s1         |s2|        s1_multiply_s2|
|---|---|---|
|[1,2,4]    |[4,2,1]|   [4,4,4]|
|[2,4,8]    |[8,4,2]|   [16,16,16]|
|[3,6,12]   |[12,6,3]|  [36,36,36]|