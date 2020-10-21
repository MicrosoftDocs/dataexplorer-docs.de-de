---
title: welch_test ()-Azure Daten-Explorer
description: In diesem Artikel wird welch_test () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 063e0db08b3566ed69957dda675082d8ea6942cf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253179"
---
# <a name="welch_test"></a>welch_test()

Berechnet den p_value der [Welch-Test-Funktion](https://en.wikipedia.org/wiki/Welch%27s_t-test)

```kusto
// s1, s2 values are from https://en.wikipedia.org/wiki/Welch%27s_t-test
print
    s1 = dynamic([27.5, 21.0, 19.0, 23.6, 17.0, 17.9, 16.9, 20.1, 21.9, 22.6, 23.1, 19.6, 19.0, 21.7, 21.4]),
    s2 = dynamic([27.1, 22.0, 20.8, 23.4, 23.4, 23.5, 25.8, 22.0, 24.8, 20.2, 21.9, 22.1, 22.9, 20.5, 24.4])
| mv-expand s1 to typeof(double), s2 to typeof(double)
| summarize m1=avg(s1), v1=variance(s1), c1=count(), m2=avg(s2), v2=variance(s2), c2=count()
| extend pValue=welch_test(m1,v1,c1,m2,v2,c2)

// pValue = 0.021
```

## <a name="syntax"></a>Syntax

`welch_test(`*mean1* `, ` *variance1* `, ` *count1* `, ` *mean2* `, ` *variance2* `, ` *COUNT2*`)`

## <a name="arguments"></a>Argumente

* *mean1*: Ausdruck, der den Mittelwert (Durchschnittswert) der ersten Reihe darstellt
* *variance1*: Ausdruck, der den Varianz Wert der ersten Reihe darstellt
* *count1*: Ausdruck, der die Anzahl der Werte in der ersten Reihe darstellt
* *mean2*: Ausdruck, der den Mittelwert (Durchschnittswert) der zweiten Reihe darstellt
* *variance2*: Ausdruck, der den Varianz Wert der zweiten Reihe darstellt
* *COUNT2*: Ausdruck, der die Anzahl der Werte in der zweiten Reihe darstellt

## <a name="returns"></a>Rückgabe

Von [Wikipedia](https://en.wikipedia.org/wiki/Welch%27s_t-test):

In Statistics ist der t-Test von welch ein zwei-Stichproben-Location-Test, der verwendet wird, um die Hypothese zu testen, dass zwei Auffüllungen gleich sind. Der t-Test von welch ist eine Anpassung des Student-t-Tests und zuverlässiger, wenn die beiden Beispiele über ungleiche Abweichungen und ungleiche Stichprobengrößen verfügen. Diese Tests werden häufig als "nicht paarweise" oder "unabhängige Stichproben"-t-Tests bezeichnet. Die Tests werden in der Regel angewendet, wenn die statistischen Einheiten, die den zwei verglichenen Beispielen zugrunde liegen, sich nicht überlappen. Der t-Test von welch ist weniger beliebt als der Student-t-Test, und er kann den Lesern weniger vertraut sein. Der Test wird auch als "welch ungleiche Unterschiede bei t-Test" oder "Ungleicher Varianzen t-Test" bezeichnet.
