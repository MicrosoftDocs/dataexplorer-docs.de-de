---
title: series_less_equals() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_less_equals() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 2dc03b1f1e24aaef4a6a006d72aeb980753e871e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508278"
---
# <a name="series_less_equals"></a>series_less_equals()

Berechnet die elementmäßige Logikoperation`<=`von zwei numerischen Reiheneingaben.

**Syntax**

`series_less_equals (`*Serie1* `,` *Serie2*`)`

**Argumente**

* *Serie1, Serie2*: Geben Sie numerische Arrays ein, um elementmäßig verglichen zu werden. Alle Argumente müssen dynamische Arrays sein. 

**Rückgabe**

Dynamisches Array von Booleans, das die berechnete elementmäßig eeinige oder gleiche Logikoperation zwischen den beiden Eingängen enthält. Jedes nicht numerische Element oder nicht vorhandene Element (Arrays `null` unterschiedlicher Größe) ergibt einen Elementwert.

**Beispiel**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_equals_s2 = series_less_equals(s1, s2)
```

|s1|s2|s1_less_equals_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[true,true,false]|

**Siehe auch**

Vergleiche in der Serienstatistik finden Sie unter:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)