---
title: series_subtract() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden series_subtract() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1e984bc35192da5d61448211c49ff582f225eb19
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507938"
---
# <a name="series_subtract"></a>series_subtract()

Berechnet die elementbezogene Subtraktion von zwei numerischen Reiheneingaben.

**Syntax**

`series_subtract(`*Serie1* `,` *Serie2*`)`

**Argumente**

* *series1, series2*: Geben Sie numerische Arrays ein, die zweiten werden elementweise von der ersten in ein dynamisches Arrayergebnis subtrahiert. Alle Argumente müssen dynamische Arrays sein. 

**Rückgabe**

Dynamisches Array des berechneten elementmäßigen Subtraktionsvorgangs zwischen den beiden Eingängen. Jedes nicht numerische Element oder nicht vorhandene Element (Arrays `null` unterschiedlicher Größe) ergibt einen Elementwert.

**Beispiel**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_subtract_s2 = series_subtract(s1, s2)
```

|s1|s2|s1_subtract_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[-3,0,3]|
|[2,4,8]|[8,4,2]|[-6,0,6]|
|[3,6,12]|[12,6,3]|[-9,0,9]|