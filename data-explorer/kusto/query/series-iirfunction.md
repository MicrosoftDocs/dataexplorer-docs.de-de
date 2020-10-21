---
title: series_iir ()-Azure Daten-Explorer
description: In diesem Artikel wird series_iir () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 3cd0559393e9c5194b06bcf93449a68b7d55cc1c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250024"
---
# <a name="series_iir"></a>series_iir()

Wendet einen unendlichen Impuls Antwort Filter auf eine Reihe an.  

Die Funktion nimmt einen Ausdruck, der das dynamische numerische Array enthält, als Eingabe an und wendet einen [unendlichen Impuls Antwort](https://en.wikipedia.org/wiki/Infinite_impulse_response) Filter an. Durch die Angabe der Filterkoeffizienten kann die-Funktion verwendet werden:
* So berechnen Sie die kumulative Summe der Reihe
* Anwenden von Glättungs Vorgängen
* So wenden Sie verschiedene [High-Pass-](https://en.wikipedia.org/wiki/High-pass_filter), [Band Pass-](https://en.wikipedia.org/wiki/Band-pass_filter)und [Low-Pass-](https://en.wikipedia.org/wiki/Low-pass_filter) Filter an

Die-Funktion übernimmt als Eingabe die Spalte, die das dynamische Array enthält, und zwei statische dynamische Arrays der *Filter-und* - *b* -Koeffizienten und wendet den Filter auf die Spalte an. Sie gibt eine neue dynamische Array-Spalte mit der gefilterten Ausgabe aus.  

## <a name="syntax"></a>Syntax

`series_iir(`*x* `,` *b* `,` *a*`)`

## <a name="arguments"></a>Argumente

* *x*: dynamische Array Zelle, die ein Array numerischer Werte ist, in der Regel die resultierende Ausgabe von [make-Series-](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md) -Operatoren.
* *b*: ein konstanter Ausdruck, der die Zähler Koeffizienten des Filters (gespeichert als dynamisches Array numerischer Werte) enthält.
* *a*: ein konstanter Ausdruck, wie z. *b. b*. Enthält die Nennerkoeffizienten des Filters.

> [!IMPORTANT]
> Das erste Element von `a` (d. h `a[0]` .) darf nicht NULL sein, um die Division durch 0 zu vermeiden. Siehe [folgende Formel](#the-filters-recursive-formula).

## <a name="the-filters-recursive-formula"></a>Die rekursive Formel des Filters.

* Angenommen, das Eingabe Array X und die Koeffizienten Arrays a und b von Längen n_a bzw. n_b. Die Übertragungsfunktion des Filters, der das Ausgabe Array Y generiert, wird definiert durch:

<div align="center">
Y<sub>i</sub> = a<sub>0</sub><sup>-1</sup>(b<sub>0</sub>x<sub>i</sub> 
 + b<sub>1</sub>x<sub>i-1</sub> + ... + b<sub>n<sub>b</sub>-1</sub>X<sub>i-n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>Y<sub>i-1</sub>-a<sub>2</sub>Y<sub>i-2</sub> - ...-a<sub>n<sub>a</sub>-1</sub>y<sub>i-n<sub>a</sub>-1</sub>)
</div>

## <a name="example"></a>Beispiel

Berechnen Sie eine kumulative Summe. Verwenden Sie den IIR-Filter mit Koeffizienten *a*= [1,-1] und *b*= [1]:  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|4,0|10.0|

Im folgenden wird beschrieben, wie Sie ihn in eine Funktion einschließen:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let vector_sum=(x:dynamic)
{
  let y=array_length(x) - 1;
  toreal(series_iir(x, dynamic([1]), dynamic([1, -1]))[y])
};
print d=dynamic([0, 1, 2, 3, 4])
| extend dd=vector_sum(d)
```

|T            |dd  |
|-------------|----|
|`[0,1,2,3,4]`|`10`|
