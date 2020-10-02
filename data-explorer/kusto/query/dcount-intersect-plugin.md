---
title: 'dcount_intersect-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird dcount_intersect-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 15720109ffa49eb89b2595d2de3105c15749960d
ms.sourcegitcommit: 58faa053eb003192db01df034cd6f5067ecc31bd
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91625447"
---
# <a name="dcount_intersect-plugin"></a>Plug-In „dcount_intersect“

Berechnet die Schnittmenge zwischen N Sätzen auf der Grundlage von `hll` Werten (n im Bereich von [2.. 16]) und gibt N- `dcount` Werte zurück.

Die angegebenen Sätze s<sub>1</sub>, s<sub>2</sub>,.. S<sub>n</sub> -gibt Werte zurück, die eine unterschiedliche Anzahl von darstellen:  
S<sub>1</sub>, s<sub>1</sub> ∩ s<sub>2</sub>,  
S<sub>1</sub> ∩ s<sub>2</sub> ∩ s<sub>3</sub>,  
... ,  
S<sub>1</sub> ∩ s<sub>2</sub> ∩... ∩ S<sub>n</sub>

```kusto
T | evaluate dcount_intersect(hll_1, hll_2, hll_3)
```

## <a name="syntax"></a>Syntax

*T* `| evaluate` `dcount_intersect(` *hll_1*, *hll_2*, [ `,` *hll_3* `,` ...]`)`

## <a name="arguments"></a>Argumente

* *T*: der tabellarische Eingabe Ausdruck.
* *hll_i*: die Werte von<sub>Set S,</sub> die mit [`hll()`](./hll-aggfunction.md) Function berechnet wurden.

## <a name="returns"></a>Gibt zurück

Gibt eine Tabelle mit N `dcount` Werten zurück (pro Spalte, die festgelegte Schnittmengen darstellen).
Spaltennamen sind S0, S1,... (bis n-1).

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net/Samples -->
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
