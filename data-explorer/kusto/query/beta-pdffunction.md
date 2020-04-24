---
title: beta_pdf() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird beta_pdf() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 53b86d88b05cc6c5cc31f1e3bbb9e3e712eed7f8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517866"
---
# <a name="beta_pdf"></a>beta_pdf()

Gibt die Betafunktion der Wahrscheinlichkeitsdichte zurück.

```kusto
beta_pdf(0.2, 10.0, 50.0)
```

Die Beta-Verteilung wird häufig verwendet, um die prozentuale Abweichung eines Werts über mehrere Stichproben hinweg zu untersuchen, etwa den Anteil der Tageszeit, die Menschen mit Fernsehen verbringen.

**Syntax**

`beta_pdf(`*x*`, `*alpha*`, `*beta*`)`

**Argumente**

* *x*: Ein Wert, bei dem die Funktion ausgewertet werden soll.
* *alpha*: Ein Parameter der Verteilung.
* *beta*: Ein Parameter der Verteilung.

**Rückgabe**

* Die [Wahrscheinlichkeit beta Dichtefunktion](https://en.wikipedia.org/wiki/Beta_distribution#Probability_density_function).

**Hinweise**

Wenn ein Argument nicht numerisch ist, gibt beta_pdf() den NULL-Wert zurück.

Wenn x 0 oder 1 x, gibt beta_pdf() den NaN-Wert zurück.

Wenn alpha 0 oder beta 0, beta_pdf() den NaN-Wert zurückgibt.

**Beispiele**

```kusto
datatable(x:double, alpha:double, beta:double, comment:string)
[
    0.5, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "x > 1, yields NaN",
    double(-10), 10.0, 20.0, "x < 0, yields NaN",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend r = beta_pdf(x, alpha, beta)
```

|x|alpha|Beta|comment|r|
|---|---|---|---|---|
|0.5|10|20|Gültige Eingabe|0.746176019310951|
|1.5|10|20|x > 1, ergibt NaN|NaN|
|-10|10|20|x < 0, ergibt NaN|NaN|
|0,1|-1|20|alpha ist < 0, ergibt NaN|NaN|

**References**

* Informationen zum Berechnen der Umkehrung der kumulativen Beta-Wahrscheinlichkeitsdichtefunktion finden Sie unter [beta-inv()](./beta-invfunction.md).
* Die standardmäßige kumulative Betaverteilungsfunktion finden Sie unter [beta-cdf()](./beta-cdffunction.md).