---
title: beta_inv() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden beta_inv() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 20bdf8bfc01ef3ac6c6a12f6a43d87fd7b5c07e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517883"
---
# <a name="beta_inv"></a>beta_inv()

Gibt die Umkehrung der Beta-Funktion für kumulative Wahrscheinlichkeit beta-Dichte zurück.

```kusto
beta_inv(0.1, 10.0, 50.0)
```

Wenn *wahrscheinlich* = `beta_cdf(`*x*,... `)`, `beta_inv(`dann *Wahrscheinlichkeit*,... `)` *x*x  = . 

Die Beta-Verteilung kann bei der Projektplanung verwendet werden, um wahrscheinliche Abschlusszeiten zu modellieren, wenn eine erwartete Abschlusszeit und Varianz vorliegen.

**Syntax**

`beta_inv(`*Wahrscheinlichkeit*`, `*alpha*`, `*beta*`)`

**Argumente**

* *Wahrscheinlichkeit*: Eine Wahrscheinlichkeit, die mit der Betaverteilung verbunden ist.
* *alpha*: Ein Parameter der Verteilung.
* *beta*: Ein Parameter der Verteilung.

**Rückgabe**

* Die Umkehrung der kumulativen Beta-Wahrscheinlichkeitsdichtefunktion [beta_cdf()](./beta-cdffunction.md)

**Hinweise**

Wenn ein Argument nicht numerisch ist, gibt beta_inv() den NULL-Wert zurück.

Wenn alpha 0 oder beta 0, beta_inv() den NULL-Wert zurückgibt.

Wenn die Wahrscheinlichkeit 0 oder die Wahrscheinlichkeit > 1, gibt beta_inv() den NaN-Wert zurück.

Bei einem Wert für die Wahrscheinlichkeit sucht beta_inv() diesen Wert x, so dass beta_cdf(x, alpha, beta) = Wahrscheinlichkeit.

**Beispiele**

```kusto
datatable(p:double, alpha:double, beta:double, comment:string)
[
    0.1, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "p > 1, yields null",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_inv(p, alpha, beta)
```

|p|alpha|Beta|comment|b|
|---|---|---|---|---|
|0,1|10|20|Gültige Eingabe|0.226415022388749|
|1.5|10|20|p > 1, ergibt null||
|0,1|-1|20|alpha ist < 0, ergibt NaN|NaN|

**Siehe auch**

* Informationen zur Berechnung der kumulativen Betaverteilungsfunktion finden Sie unter [beta-cdf()](./beta-cdffunction.md).
* Informationen zur Berechnung der Wahrscheinlichkeits-Betadichtefunktion finden Sie unter [beta-pdf()](./beta-pdffunction.md).