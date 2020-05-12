---
title: beta_inv ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird beta_inv () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 60b054bbd234a77f81c47e375b98be0a5df103a5
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227654"
---
# <a name="beta_inv"></a>beta_inv()

Gibt die Umkehrung der kumulativen Beta-Wahrscheinlichkeitsfunktion der Beta-Funktion zurück.

```kusto
beta_inv(0.1, 10.0, 50.0)
```

Wenn *Wahrscheinlichkeit*  =  `beta_cdf(` *x*,... `)` , dann `beta_inv(` *Wahrscheinlichkeit*,... `)`  =  *x*. 

Die Beta-Verteilung kann bei der Projektplanung verwendet werden, um wahrscheinliche Abschlusszeiten zu modellieren, wenn eine erwartete Abschlusszeit und Varianz vorliegen.

**Syntax**

`beta_inv(`*Wahrscheinlichkeit* `, ` *Alpha* `, ` *Beta Version*`)`

**Argumente**

* *Wahrscheinlichkeit*: eine der Beta-Verteilung zugeordnete Wahrscheinlichkeit.
* *Alpha*: ein Parameter der Verteilung.
* *Beta*: ein Parameter der Verteilung.

**Rückgabe**

* Die Umkehrung der kumulativen Beta-Wahrscheinlichkeitsdichte-Funktion [beta_cdf ()](./beta-cdffunction.md) .

**Hinweise**

Wenn ein Argument nicht numerisch ist, gibt beta_inv () einen NULL-Wert zurück.

Wenn Alpha-0 oder Beta-0, beta_inv () den NULL-Wert zurückgibt.

Wenn die Wahrscheinlichkeit 0 (null) oder die Wahrscheinlichkeit > 1 ist, gibt beta_inv () den NaN-Wert zurück.

Bei Angabe eines Werts für die Wahrscheinlichkeit sucht beta_inv () den Wert x, sodass beta_cdf (x, Alpha, Beta) = Wahrscheinlichkeit ist.

**Beispiele**

<!-- csl: https://help.kusto.windows.net/Samples -->
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
|1.5|10|20|p > 1, ergibt NULL||
|0,1|-1|20|Alpha ist < 0, ergibt Nan|NaN|

**Siehe auch**

* Informationen zum Berechnen der kumulativen Beta Verteilungsfunktion finden Sie unter [Beta-CDF ()](./beta-cdffunction.md).
* Informationen zum Berechnen der Wahrscheinlichkeits-Beta Dichtefunktion finden Sie unter [Beta-PDF ()](./beta-pdffunction.md).
