---
title: series_greater_equals() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_greater_equals() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 21a1e4d972c06f212344a665c6b3320046ee93c8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508397"
---
# <a name="series_greater_equals"></a>series_greater_equals()

Berechnet die elementmäßig eisern`>=`größere oder gleiche ( ) Logikoperation von zwei numerischen Reiheneingaben.

**Syntax**

`series_greater_equals (`*Serie1* `,` *Serie2*`)`

**Argumente**

* *Serie1, Serie2*: Geben Sie numerische Arrays ein, um elementmäßig verglichen zu werden. Alle Argumente müssen dynamische Arrays sein. 

**Rückgabe**

Dynamisches Array von Booleans, das die berechnete elementweise größere oder gleiche Logikoperation zwischen den beiden Eingängen enthält. Jedes nicht numerische Element oder nicht vorhandene Element (Arrays `null` unterschiedlicher Größe) ergibt einen Elementwert.

**Beispiel**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_equals_s2 = series_greater_equals(s1, s2)
```

|s1|s2|s1_greater_equals_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[false,true,true]|

**Siehe auch**

Vergleiche in der Serienstatistik finden Sie unter:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)