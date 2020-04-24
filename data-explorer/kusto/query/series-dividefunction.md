---
title: series_divide() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_divide() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8e8b806c325da9bfce5f79ce5a5c4e5cfadaa838
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508839"
---
# <a name="series_divide"></a>series_divide()

Berechnet die elementweise Aufteilung zweier numerischer Reiheneingaben.

**Syntax**

`series_divide(`*Serie1* `,` *Serie2*`)`

**Argumente**

* *series1, series2*: Geben Sie numerische Arrays ein, die ersten, die elementmäßig geteilt werden, durch das zweite in ein dynamisches Arrayergebnis. Alle Argumente müssen dynamische Arrays sein. 

**Rückgabe**

Dynamisches Array der berechneten elementweisen Teilungsoperation zwischen den beiden Eingängen. Jedes nicht numerische Element oder nicht vorhandene Element (Arrays `null` unterschiedlicher Größe) ergibt einen Elementwert.

Hinweis: Die Ergebnisreihe ist vom doppelten Typ, auch wenn es sich bei den Eingaben um ganze Zahlen handelt. Division durch Null folgt der doppelten Division durch Null (z.B. 2/0 ergibt double(+inf)).

**Beispiel**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1,2,4]    |[4,2,1]|   [0.25,1.0,4.0]|
|[2,4,8]    |[8,4,2]|   [0.25,1.0,4.0]|
|[3,6,12]   |[12,6,3]|  [0.25,1.0,4.0]|