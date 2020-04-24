---
title: dcount_intersect-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird dcount_intersect-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7771456ffa75085c79933c2e789e3d98f352b76f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516132"
---
# <a name="dcount_intersect-plugin"></a>dcount_intersect Plugin

Berechnet den Schnittpunkt zwischen N-Sets basierend auf hll-Werten (N im Bereich von [2..16]) und gibt N-Dcount-Werte zurück.

Gegebene Sätze S<sub>1</sub>, S<sub>2</sub>, .. S<sub>n</sub> - Rückgabewerte stellen unterschiedliche Zählungen dar:  
S<sub>1</sub>, S<sub>1</sub> s<sub>2</sub>,  
S<sub>1</sub> s<sub>2</sub> s<sub>3</sub>,  
... ,  
S<sub>1</sub> - S<sub>2</sub> - ... S<sub>n</sub>

    T | evaluate dcount_intersect(hll_1, hll_2, hll_3)

**Syntax**

*T* `| evaluate` T `dcount_intersect(` *hll_1*,`,` *hll_2*, [ *hll_3* `,` ...]`)`

**Argumente**

* *T*: Der tabellarische Eingabeausdruck.
* *hll_i*: die Werte des Satzes S<sub>i,</sub> berechnet mit [hll()-Funktion.](./hll-aggfunction.md)

**Rückgabe**

Gibt eine Tabelle mit N-Dcount-Werten zurück (pro Spaltenspalten, die Schnittpunkte darstellen).
Spaltennamen sind s0, s1, ... (bis n-1).

**Beispiele**

```kusto
// Generate numbers from 1 to 100
range x from 1 to 100 step 1
| extend isEven = (x % 2 == 0), isMod3 = (x % 3 == 0), isMod5 = (x % 5 == 0)
// Calculate conditional HLL values (note that '0' is included in each of them as additional value, so we will subtract it later)
| summarize hll_even = hll(iif(isEven, x, 0), 2),
            hll_mod3 = hll(iif(isMod3, x, 0), 2),
            hll_mod5 = hll(iif(isMod5, x, 0), 2) 
// Invoke the plugin that calculates dcount intersections         
| evaluate dcount_intersect(hll_even, hll_mod3, hll_mod5)
| project evenNumbers = s0 - 1,             //                             100 / 2 = 50
          even_and_mod3 = s1 - 1,           // gcd(2,3) = 6, therefor:     100 / 6 = 16
          even_and_mod3_and_mod5 = s2 - 1   // gcd(2,3,5) is 30, therefore: 100 / 30 = 3 
```

|evenNumbers|even_and_mod3|even_and_mod3_and_mod5|
|---|---|---|
|50|16|3|