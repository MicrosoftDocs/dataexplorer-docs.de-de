---
title: beta_cdf ()-Azure Daten-Explorer
description: In diesem Artikel wird beta_cdf () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a98fe59755e47be8f4f4e53595d25bb260004236
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349226"
---
# <a name="beta_cdf"></a>beta_cdf()

Gibt die standardmäßige kumulative Beta Verteilungsfunktion zurück.

```kusto
beta_cdf(0.2, 10.0, 50.0)
```

Wenn *Wahrscheinlichkeit*  =  `beta_cdf(` *x*,... `)` , dann `beta_inv(` *Wahrscheinlichkeit*,... `)`  =  *x*.

Die Beta-Verteilung wird häufig verwendet, um die prozentuale Abweichung eines Werts über mehrere Stichproben hinweg zu untersuchen, etwa den Anteil der Tageszeit, die Menschen mit Fernsehen verbringen.

## <a name="syntax"></a>Syntax

`beta_cdf(`*x* `, ` *Alpha* `, ` *Beta Version*`)`

## <a name="arguments"></a>Argumente

* *x*: ein Wert, bei dem die Funktion ausgewertet werden soll.
* *Alpha*: ein Parameter der Verteilung.
* *Beta*: ein Parameter der Verteilung.

## <a name="returns"></a>Rückgabe

* Die [kumulative Beta Verteilungsfunktion](https://en.wikipedia.org/wiki/Beta_distribution#Cumulative_distribution_function).

**Hinweise**

Wenn ein Argument nicht numerisch ist, gibt beta_cdf () einen NULL-Wert zurück.

Wenn x < 0 oder x > 1, gibt beta_cdf () den NaN-Wert zurück.

Wenn Alpha-0 oder Beta-0, beta_cdf () den NaN-Wert zurückgibt.

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(x:double, alpha:double, beta:double, comment:string)
[
    0.9, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "x > 1, yields NaN",
    double(-10), 10.0, 20.0, "x < 0, yields NaN",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_cdf(x, alpha, beta)
```

|x|alpha|Beta|comment|b|
|---|---|---|---|---|
|0.9|10|20|Gültige Eingabe|0.999999999999959|
|1.5|10|20|x > 1, ergibt Nan|NaN|
|-10|10|20|x < 0, ergibt Nan|NaN|
|0,1|-1|20|Alpha ist < 0, ergibt Nan|NaN|


**Weitere Informationen**


* Informationen zum Berechnen der Umkehrung der kumulativen Beta Wahrscheinlichkeitsdichte-Funktion finden Sie unter [Beta-Inv ()](./beta-invfunction.md).
* Informationen zum Berechnen der Wahrscheinlichkeitsdichte-Funktion finden Sie unter [Beta-PDF ()](./beta-pdffunction.md).