---
title: beta_cdf() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird beta_cdf() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4faaeddc647d047755108f3c993db855a56de363
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517917"
---
# <a name="beta_cdf"></a>beta_cdf()

Gibt die standardmäßige kumulative Betaverteilungsfunktion zurück.

```kusto
beta_cdf(0.2, 10.0, 50.0)
```

Wenn *wahrscheinlich* = `beta_cdf(`*x*,... `)`, `beta_inv(`dann *Wahrscheinlichkeit*,... `)` *x*x  = .

Die Beta-Verteilung wird häufig verwendet, um die prozentuale Abweichung eines Werts über mehrere Stichproben hinweg zu untersuchen, etwa den Anteil der Tageszeit, die Menschen mit Fernsehen verbringen.

**Syntax**

`beta_cdf(`*x*`, `*alpha*`, `*beta*`)`

**Argumente**

* *x*: Ein Wert, bei dem die Funktion ausgewertet werden soll.
* *alpha*: Ein Parameter der Verteilung.
* *beta*: Ein Parameter der Verteilung.

**Rückgabe**

* Die [kumulative Betaverteilungsfunktion](https://en.wikipedia.org/wiki/Beta_distribution#Cumulative_distribution_function).

**Hinweise**

Wenn ein Argument nicht numerisch ist, gibt beta_cdf() den NULL-Wert zurück.

Wenn x 0 oder x > 1 <, gibt beta_cdf() den NaN-Wert zurück.

Wenn alpha 0 oder beta 0, beta_cdf() den NaN-Wert zurückgibt.

**Beispiele**

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
|1.5|10|20|x > 1, ergibt NaN|NaN|
|-10|10|20|x < 0, ergibt NaN|NaN|
|0,1|-1|20|alpha ist < 0, ergibt NaN|NaN|


**Siehe auch**


* Informationen zum Berechnen der Umkehrung der kumulativen Beta-Wahrscheinlichkeitsdichtefunktion finden Sie unter [beta-inv()](./beta-invfunction.md).
* Die Funktion zur Berechnung der Wahrscheinlichkeitsdichte finden Sie unter [beta-pdf()](./beta-pdffunction.md).