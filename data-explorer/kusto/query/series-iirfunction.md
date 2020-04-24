---
title: series_iir() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_iir() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 96452265e07a8a8b057dc70bec520be6ab298dd3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508295"
---
# <a name="series_iir"></a>series_iir()

Wendet einen Infinite Impulse Response-Filter auf eine Serie an.  

Nimmt einen Ausdruck, der dynamische summische Arrays enthält, als Eingabe und wendet einen [Infinite Impulse Response-Filter](https://en.wikipedia.org/wiki/Infinite_impulse_response) an. Durch die Angabe der Filterkoeffizienten kann es z. B. verwendet werden, um die kumulative Summe der Serie zu berechnen, Glättungsvorgänge sowie verschiedene [Hochpass-,](https://en.wikipedia.org/wiki/High-pass_filter) [Bandpass-](https://en.wikipedia.org/wiki/Band-pass_filter) und [Tiefpassfilter](https://en.wikipedia.org/wiki/Low-pass_filter) anzuwenden. Die Funktion nimmt die Spalte, die das dynamische Array und zwei statische dynamische Arrays der *a-* und *b-Koeffizienten* des Filters enthält, als Eingabe und wendet den Filter auf die Spalte an. Sie gibt eine neue dynamische Array-Spalte mit der gefilterten Ausgabe aus.  
 

**Syntax**

`series_iir(`*x* `,` *b* `,` *a*`)`

**Argumente**

* *x*: Dynamische Arrayzelle, die ein Array numerischer Werte ist, in der Regel die resultierende Ausgabe von [Make-Series-](make-seriesoperator.md) oder make_list-Operatoren. [make_list](makelist-aggfunction.md)
* *b*: Ein konstanter Ausdruck, der die Zählerkoeffizienten des Filters enthält (gespeichert als dynamisches Array numerischer Werte).
* *a*: Ein konstanter Ausdruck, wie *b*. Enthält die Nennerkoeffizienten des Filters.

> [!IMPORTANT]
> Das erste `a` Element von (d.h. `a[0]`) darf nicht Null sein (um eine Teilung durch 0 zu vermeiden; siehe die Formel unten).

**Weitere Informationen zur rekursiven Formel des Filters**

* Bei einem Eingabe-Array X und Koeffizienten-Arrays a, b der Längen n_a bzw. n_b wird die Übertragungsfunktion des Filters, die das Ausgabe-Array Y generiert, durch (siehe auch in Wikipedia) definiert:

<div align="center">
Y<sub>i</sub> = a<sub>0</sub><sup>-1</sup>(b<sub>0</sub>X<sub>i</sub> 
 + b<sub>1</sub>X<sub>i-1</sub> + ... + b<sub>n<sub>b</sub>-1</sub>X<sub>i-n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>Y<sub>i-1</sub>-a<sub>2</sub>Y<sub>i-2</sub> - ... - a<sub>n<sub>a</sub>-1</sub>Y<sub>i-n<sub>a</sub>-1</sub>)
</div>

**Beispiel**

Die Berechnung der kumulativen Summe kann durch iir-Filter mit den Koeffizienten *a*=[1,-1] und *b*=[1] durchgeführt werden:  

```kusto
let x = range(1.0, 10, 1);
print x=x, y = series_iir(x, dynamic([1]), dynamic([1,-1]))
| mv-expand x, y
```

| x | y |
|:--|:--|
|1.0|1.0|
|2.0|3.0|
|3.0|6.0|
|4,0|10,0|

So wickeln Sie es in eine Funktion ein:

```kusto
let vector_sum=(x:dynamic)
{
  let y=array_length(x) - 1;
  toreal(series_iir(x, dynamic([1]), dynamic([1, -1]))[y])
};
print d=dynamic([0, 1, 2, 3, 4])
| extend dd=vector_sum(d)
```

|d            |dd  |
|-------------|----|
|`[0,1,2,3,4]`|`10`|