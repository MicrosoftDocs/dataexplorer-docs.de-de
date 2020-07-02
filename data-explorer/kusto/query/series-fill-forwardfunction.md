---
title: series_fill_forward ()-Azure Daten-Explorer
description: In diesem Artikel wird series_fill_forward () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7ea5210f0370b495c48615d28e763bf6e396d46e
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763692"
---
# <a name="series_fill_forward"></a>series_fill_forward()

Führt eine vorwärts Füll interpolung von fehlenden Werten in einer Reihe aus.

Ein Ausdruck, der das dynamische numerische Array enthält, ist die Eingabe. Die-Funktion ersetzt alle Instanzen von missing_value_placeholder durch den nächstgelegenen Wert von der linken neben missing_value_placeholder und gibt das resultierende Array zurück. Die am weitesten links stehenden Instanzen von missing_value_placeholder werden beibehalten.

**Syntax**

`series_fill_forward(`*x* `[, ` *missing_value_placeholder*`])`
* Gibt Series *x* zurück, wobei alle Instanzen von *missing_value_placeholder* ausgefüllt werden.

**Argumente**

* *x*: Skalarausdruck des dynamischen Arrays, bei dem es sich um ein Array numerischer Werte handelt. 
* *missing_value_placeholder*: optionaler Parameter, der einen Platzhalter für einen fehlenden zu ersetzenden Wert angibt. Der Standardwert ist `double` (*null*).

**Notizen**

* Geben Sie *null* als Standardwert an, um Interpolations Funktionen nach der [make-Reihe](make-seriesoperator.md)anzuwenden: 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```

* Der *missing_value_placeholder* kann einen beliebigen Typ aufweisen, der in tatsächliche Elementtypen konvertiert wird. Beide `double` (*null*) `long` (*null*) und `int` (*null*) haben dieselbe Bedeutung.
* Wenn missing_value_placeholder (null) ist (oder weggelassen wird, die dieselbe Bedeutung haben), kann ein Ergebnis *null* -Werte enthalten. Um diese *null* -Werte auszufüllen, verwenden Sie andere Interpolations Funktionen. Derzeit unterstützen nur [series_outliers ()](series-outliersfunction.md) *null* -Werte in Eingabe Arrays.
* Die-Funktionen erhalten den ursprünglichen Typ von Array Elementen.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_forward(arr)  

```

|`arr`|`fill_forward`|
|---|---|
|[NULL, NULL, 36, 41, NULL, NULL, 16, 61, 33, NULL, NULL]|[NULL, NULL, 36, 41, 41, 41, 16, 61, 33, 33, 33]|
   
Verwenden Sie [series_fill_backward](series-fill-backwardfunction.md) oder [Series-Fill-Konstanten](series-fill-constfunction.md) , um die interpolung des obigen Arrays abzuschließen.
