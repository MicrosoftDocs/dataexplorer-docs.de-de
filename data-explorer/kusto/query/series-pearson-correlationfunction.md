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
ms.openlocfilehash: 3b65dff40e644852555465fe6ce07ed94c4920ea
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351317"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

Berechnet den Pearson-Korrelationskoeffizienten von zwei numerischen Reihen Eingaben.

Siehe: [Pearson-Korrelationskoeffizient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient).

## <a name="syntax"></a>Syntax

`series_pearson_correlation(`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Argumente

* *Series1, series2*: numerische Eingabe Arrays für die Berechnung des Korrelationskoeffizient. Bei allen Argumenten muss es sich um dynamische Arrays gleicher Länge handeln. 

## <a name="returns"></a>Rückgabe

Der berechnete Pearson-Korrelationskoeffizienten zwischen den zwei Eingaben. Ein nicht numerisches Element oder ein nicht vorhandenes Element (Arrays mit unterschiedlichen Größen) ergeben ein `null` Ergebnis.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1, 2, 3, 4, 5]|[2, 4, 6, 8, 10]|1|
