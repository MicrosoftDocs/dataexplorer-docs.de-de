---
title: series_pearson_correlation ()-Azure Daten-Explorer
description: In diesem Artikel wird series_pearson_correlation () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: 9187c10ad62b4d925bf6211e64657fba5ae17b63
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372511"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

Berechnet den Pearson-Korrelationskoeffizienten von zwei numerischen Reihen Eingaben.

Siehe: [Pearson-Korrelationskoeffizient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient).

**Syntax**

`series_pearson_correlation(`*Series1* `,` *Series2*`)`

**Argumente**

* *Series1, series2*: numerische Eingabe Arrays für die Berechnung des Korrelationskoeffizient. Bei allen Argumenten muss es sich um dynamische Arrays gleicher Länge handeln. 

**Rückgabe**

Der berechnete Pearson-Korrelationskoeffizienten zwischen den zwei Eingaben. Ein nicht numerisches Element oder ein nicht vorhandenes Element (Arrays mit unterschiedlichen Größen) ergeben ein `null` Ergebnis.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1, 2, 3, 4, 5]|[2, 4, 6, 8, 10]|1|
