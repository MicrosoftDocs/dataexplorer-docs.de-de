---
title: beta_pdf ()-Azure Daten-Explorer
description: In diesem Artikel wird beta_pdf () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ef26e3a24d7bc6f1d4cd50f74ca83d0a1d21dab
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245490"
---
# <a name="beta_pdf"></a>beta_pdf()

Gibt die Beta Funktion der Wahrscheinlichkeitsdichte zurück.

```kusto
beta_pdf(0.2, 10.0, 50.0)
```

Die Beta-Verteilung wird häufig verwendet, um die prozentuale Abweichung eines Werts über mehrere Stichproben hinweg zu untersuchen, etwa den Anteil der Tageszeit, die Menschen mit Fernsehen verbringen.

## <a name="syntax"></a>Syntax

`beta_pdf(`*x* `, ` *Alpha* `, ` *Beta Version*`)`

## <a name="arguments"></a>Argumente

* *x*: ein Wert, bei dem die Funktion ausgewertet werden soll.
* *Alpha*: ein Parameter der Verteilung.
* *Beta*: ein Parameter der Verteilung.

## <a name="returns"></a>Rückgabe

* Die [Wahrscheinlichkeits-Beta-Funktion](https://en.wikipedia.org/wiki/Beta_distribution#Probability_density_function).

**Notizen**

Wenn ein Argument nicht numerisch ist, gibt beta_pdf () einen NULL-Wert zurück.

Wenn x, 0 oder 1. x, beta_pdf () den NaN-Wert zurückgibt.

Wenn Alpha-0 oder Beta-0, beta_pdf () den NaN-Wert zurückgibt.

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net/Samples -->
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
|1.5|10|20|x > 1, ergibt Nan|NaN|
|–10|10|20|x < 0, ergibt Nan|NaN|
|0.1|-1|20|Alpha ist < 0, ergibt Nan|NaN|

**Referenzen**

* Informationen zum Berechnen der Umkehrung der kumulativen Beta Wahrscheinlichkeitsdichte-Funktion finden Sie unter [Beta-Inv ()](./beta-invfunction.md).
* Die standardmäßige kumulative Beta-Verteilungsfunktion finden Sie unter [Beta-CDF ()](./beta-cdffunction.md).
