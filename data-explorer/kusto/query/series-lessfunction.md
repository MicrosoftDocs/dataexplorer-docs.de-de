---
title: series_less() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden series_less() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 8fc09141a6e60489ff2be246e145876357141785
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508261"
---
# <a name="series_less"></a>series_less()

Berechnet die elementmäßig weniger`<`( ) Logikoperation von zwei numerischen Reiheneingaben.

**Syntax**

`series_less (`*Serie1* `,` *Serie2*`)`

**Argumente**

* *Serie1, Serie2*: Geben Sie numerische Arrays ein, um elementmäßig verglichen zu werden. Alle Argumente müssen dynamische Arrays sein. 

**Rückgabe**

Dynamisches Array von Booleans, das die berechnete elementmäßig weniger logische Operation zwischen den beiden Eingängen enthält. Jedes nicht numerische Element oder nicht vorhandene Element (Arrays `null` unterschiedlicher Größe) ergibt einen Elementwert.

**Beispiel**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_s2 = series_less(s1, s2)
```

|s1|s2|s1_less_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[true,false,false]|

**Siehe auch**

Vergleiche in der Serienstatistik finden Sie unter:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)