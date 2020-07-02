---
title: series_fill_linear ()-Azure Daten-Explorer
description: In diesem Artikel wird series_fill_linear () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4bf68800cc10bf301f6a5738d47e670905c2c46d
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763654"
---
# <a name="series_fill_linear"></a>series_fill_linear()

Linearly interpoliert fehlende Werte in einer Reihe.

Nimmt einen Ausdruck, der das dynamische numerische Array enthält, als Eingabe an, führt die lineare Interpolationen für alle Instanzen missing_value_placeholder aus und gibt das resultierende Array zurück. Wenn der Anfang und das Ende des Arrays missing_value_placeholder enthalten, wird es durch den nächstgelegenen Wert ersetzt, der nicht missing_value_placeholder ist. Diese Funktion kann ausgeschaltet werden. Wenn das gesamte Array aus dem missing_value_placeholder besteht, wird das Array mit constant_value gefüllt, oder 0, wenn es nicht angegeben ist.  

**Syntax**

`series_fill_linear(`*x* `[,` *missing_value_placeholder* ` [,` *fill_edges* ` [,` *constant_value*`]]]))`
* Gibt die Reihen lineare interpolung von *x* unter Verwendung der angegebenen Parameter zurück.
 

**Argumente**

* *x*: Skalarausdruck des dynamischen Arrays, bei dem es sich um ein Array numerischer Werte handelt.
* *missing_value_placeholder*: optionaler Parameter, der einen Platzhalter für die zu ersetzenden "fehlenden Werte" angibt. Der Standardwert ist `double` (*null*).
* *fill_edges*: ein boolescher Wert, der angibt, ob *missing_value_placeholder* am Anfang und am Ende des Arrays durch den nächstgelegenen Wert ersetzt werden sollen. Standardmäßig " *true* ". Wenn *false*festgelegt ist, werden *missing_value_placeholder* am Anfang und am Ende des Arrays beibehalten.
* *constant_value*: optionaler Parameter, der nur für Arrays relevant ist, besteht aus *null* -Werten. Dieser Parameter gibt einen konstanten Wert an, mit dem die Reihe aufgefüllt werden soll. Der Standardwert ist *0*. Wenn dieser Parameter auf `double` (*null*) festgelegt wird, werden *null* -Werte in diesem Fall nicht angezeigt.

**Notizen**

* Geben Sie *null* als Standardwert an, um nach der [make-Reihe](make-seriesoperator.md)Interpolations Funktionen anzuwenden: 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```

* Der *missing_value_placeholder* kann einen beliebigen Typ aufweisen, der in tatsächliche Elementtypen konvertiert wird. Daher `double` haben entweder (*null*), `long` (*null*) oder `int` (*null*) dieselbe Bedeutung.
* Wenn *missing_value_placeholder* `double` (*null*) ist (oder weggelassen wird, das die gleiche Bedeutung hat), kann ein Ergebnis *null* -Werte enthalten. Verwenden Sie andere Interpolations Funktionen, um diese *null* -Werte auszufüllen. Derzeit unterstützen nur [series_outliers ()](series-outliersfunction.md) *null* -Werte in Eingabe Arrays.
* Die-Funktion behält den ursprünglichen Typ von Array Elementen bei. Wenn x nur int-oder Long-Elemente enthält, gibt die lineare interpolung gerundete, nicht exakte Werte zurück.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null, 111.0, null, 36.0, 41.0, null, null, 16.0, 61.0, 33.0, null, null]), // Array of double    
    dynamic([null, 111,   null, 36,   41,   null, null, 16,   61,   33,   null, null]), // Similar array of int
    dynamic([null, null, null, null])                                                   // Array with missing values only
];
data
| project arr, 
          without_args = series_fill_linear(arr),
          with_edges = series_fill_linear(arr, double(null), true),
          wo_edges = series_fill_linear(arr, double(null), false),
          with_const = series_fill_linear(arr, double(null), true, 3.14159)  

```

|`arr`|`without_args`|`with_edges`|`wo_edges`|`with_const`|
|---|---|---|---|---|
|[NULL, 111.0, NULL, 36,0, 41,0, NULL, NULL, 16,0, 61,0, 33,0, NULL, NULL]|[111.0, 111.0, 73,5, 36,0, 41,0, 32.667, 24.333, 16,0, 61,0, 33,0, 33,0, 33,0]|[111.0, 111.0, 73,5, 36,0, 41,0, 32.667, 24.333, 16,0, 61,0, 33,0, 33,0, 33,0]|[NULL, 111.0, 73,5, 36,0, 41,0, 32.667, 24.333, 16,0, 61,0, 33,0, NULL, NULL]|[111.0, 111.0, 73,5, 36,0, 41,0, 32.667, 24.333, 16,0, 61,0, 33,0, 33,0, 33,0]|
|[NULL, 111, NULL, 36, 41, NULL, NULL, 16, 61, 33, NULL, NULL]|[111111, 73, 36, 41, 32, 24, 16, 61, 33, 33, 33]|[111111, 73, 36, 41, 32, 24, 16, 61, 33, 33, 33]|[NULL, 111, 73, 36, 41, 32, 24, 16, 61, 33, NULL, NULL]|[111111, 74, 38, 41, 32, 24, 16, 61, 33, 33, 33]|
|[NULL, NULL, NULL, NULL]|[0,0, 0,0, 0,0, 0,0]|[0,0, 0,0, 0,0, 0,0]|[0,0, 0,0, 0,0, 0,0]|[Weise 3,14159, Weise 3,14159, Weise 3,14159, Weise 3,14159]|
