---
title: welch_test() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird welch_test() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a9f4b3d86bf3d679fd4fdd320b394956c71d3e97
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504487"
---
# <a name="welch_test"></a>welch_test()

Berechnet die p_value der [Welch-Testfunktion](https://en.wikipedia.org/wiki/Welch%27s_t-test)

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

**Syntax**

`welch_test(`*mittel1*`, `*Varianz1*`, `*anzahl1*`, `*mittel2*`, `*varianz2*`, `*anzahl2*`)`

**Argumente**

* *mean1*: Ausdruck, der den Mittelwert (Durchschnitt) der 1. Reihe darstellt
* *Varianz1*: Ausdruck, der den Abweichungswert der 1. Reihe darstellt
* *count1*: Ausdruck, der die Anzahl der Werte in der 1. Reihe darstellt
* *mean2*: Ausdruck, der den Mittelwert (Durchschnitt) der 2. Reihe darstellt
* *Varianz2*: Ausdruck, der den Abweichungswert der 2. Reihe darstellt
* *count2*: Ausdruck, der die Anzahl der Werte in der 2. Reihe darstellt

**Rückgabe**

Aus [Wikipedia](https://en.wikipedia.org/wiki/Welch%27s_t-test):

In der Statistik ist Welchs t-Test, oder ungleiche Varianzen t-Test, ein Zwei-Probe-Standorttest, der verwendet wird, um die Hypothese zu testen, dass zwei Populationen gleiche Mittel haben. Welchs t-Test ist eine Adaption des T-Tests von Student, das heißt, er wurde mit Hilfe des T-Tests von Student abgeleitet und ist zuverlässiger, wenn die beiden Proben ungleiche Abweichungen und ungleiche Stichprobengrößen aufweisen. Diese Tests werden häufig als "ungepaarte" oder "unabhängige Stichproben" t-Tests bezeichnet, da sie in der Regel angewendet werden, wenn die statistischen Einheiten, die den beiden verglichenen Stichproben zugrunde liegen, nicht überlappend sind. Angesichts der Tatsache, dass Welchs T-Test weniger beliebt war als der T-Test von Student und den Lesern vielleicht weniger vertraut ist, ist ein informativerer Name "Welchs ungleiche Varianzen t-Test" oder "ungleiche Varianzen t-Test" für Kürze.