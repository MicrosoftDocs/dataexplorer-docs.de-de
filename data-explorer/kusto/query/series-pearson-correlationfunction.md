---
title: series_pearson_correlation() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_pearson_correlation() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: 6454ec528e7a9e53b2feab5a7fefa1236ed80cdf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508108"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

Berechnet den Pearson-Korrelationskoeffizienten von zwei numerischen Reiheneingaben.

Siehe: [Pearson-Korrelationskoeffizient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient).

**Syntax**

`series_pearson_correlation(`*Serie1* `,` *Serie2*`)`

**Argumente**

* *Serie1, Serie2*: Geben Sie numerische Arrays zur Berechnung des Korrelationskoeffizienten ein. Alle Argumente müssen dynamische Arrays gleicher Länge sein. 

**Rückgabe**

Der berechnete Pearson-Korrelationskoeffizient zwischen den beiden Eingängen. Jedes nicht numerische Element oder nicht vorhandene Element (Arrays `null` unterschiedlicher Größe) ergibt ein Ergebnis.

**Beispiel**

```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1,2,3,4,5]|[2,4,6,8,10]|1|
