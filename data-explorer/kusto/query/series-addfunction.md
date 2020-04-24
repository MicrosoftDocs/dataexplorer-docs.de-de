---
title: series_add() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_add() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ea8c391060e487413a166c236abf789edcba0a0c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509060"
---
# <a name="series_add"></a>series_add()

Berechnet die elementmäßige Hinzufügung von zwei numerischen Reiheneingaben.

**Syntax**

`series_add(`*Serie1* `,` *Serie2*`)`

**Argumente**

* *series1, series2*: Geben Sie numerische Arrays ein, die elementweise in ein dynamisches Arrayergebnis eingefügt werden sollen. Alle Argumente müssen dynamische Arrays sein. 

**Rückgabe**

Dynamisches Array berechneter elementweiser Add-Operation zwischen den beiden Eingängen. Jedes nicht numerische Element oder nicht vorhandene Element (Arrays `null` unterschiedlicher Größe) ergibt einen Elementwert.

**Beispiel**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_add_s2 = series_add(s1, s2)
```

|s1|s2|s1_add_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[5,4,5]|
|[2,4,8]|[8,4,2]|[10,8,10]|
|[3,6,12]|[12,6,3]|[15,12,15]|