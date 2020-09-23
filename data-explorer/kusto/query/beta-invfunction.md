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
ms.openlocfilehash: deb91e6131d5662017ebdf714a79d0ee391c8ba1
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103303"
---
# <a name="beta_inv"></a>beta_inv()

Gibt die Umkehrung der kumulativen Beta-Wahrscheinlichkeitsfunktion der Beta-Funktion zurück.

```kusto
beta_inv(0.1, 10.0, 50.0)
```

Wenn *Wahrscheinlichkeit*  =  `beta_cdf(` *x*,... `)` , dann `beta_inv(` *Wahrscheinlichkeit*,... `)`  =  *x*. 

Die Beta-Verteilung kann bei der Projektplanung verwendet werden, um wahrscheinliche Abschlusszeiten zu modellieren, wenn eine erwartete Abschlusszeit und Varianz vorliegen.

## <a name="syntax"></a>Syntax

`beta_inv(`*Wahrscheinlichkeit* `, ` *Alpha* `, ` *Beta Version*`)`

## <a name="arguments"></a>Argumente

* *Wahrscheinlichkeit*: eine der Beta-Verteilung zugeordnete Wahrscheinlichkeit.
* *Alpha*: ein Parameter der Verteilung.
* *Beta*: ein Parameter der Verteilung.

## <a name="returns"></a>Gibt zurück

* Die Umkehrung der kumulativen Beta-Wahrscheinlichkeitsdichte-Funktion [beta_cdf ()](./beta-cdffunction.md) .

**Notizen**

Wenn ein Argument nicht numerisch ist, gibt beta_inv () einen NULL-Wert zurück.

Wenn Alpha-0 oder Beta-0, beta_inv () den NULL-Wert zurückgibt.

Wenn die Wahrscheinlichkeit 0 (null) oder die Wahrscheinlichkeit > 1 ist, gibt beta_inv () den NaN-Wert zurück.

Bei Angabe eines Werts für die Wahrscheinlichkeit sucht beta_inv () den Wert x, sodass beta_cdf (x, Alpha, Beta) = Wahrscheinlichkeit ist.

## <a name="examples"></a>Beispiele

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

|p|alpha|Beta|comment|k|
|---|---|---|---|---|
|0.1|10|20|Gültige Eingabe|0.226415022388749|
|1.5|10|20|p > 1, ergibt NULL||
|0.1|-1|20|Alpha ist < 0, ergibt Nan|NaN|

## <a name="see-also"></a>Weitere Informationen

* Informationen zum Berechnen der kumulativen Beta Verteilungsfunktion finden Sie unter [Beta-CDF ()](./beta-cdffunction.md).
* Informationen zum Berechnen der Wahrscheinlichkeits-Beta Dichtefunktion finden Sie unter [Beta-PDF ()](./beta-pdffunction.md).
