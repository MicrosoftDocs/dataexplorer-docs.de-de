---
title: series_fill_linear() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_fill_linear() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4f9a12d172a1580d6a9e575b78b14404dce7820e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508652"
---
# <a name="series_fill_linear"></a>series_fill_linear()

Führt eine lineare Interpolation fehlender Werte in einer Reihe durch.

Nimmt einen Ausdruck, der dynamisches numerisches Array als Eingabe enthält, führt eine lineare Interpolation für alle Instanzen von missing_value_placeholder durch und gibt das resultierende Array zurück. Wenn der Anfang und das Ende des Arrays missing_value_placeholder enthält, wird es durch den nächsten Wert ersetzt, der nicht missing_value_placeholder (kann deaktiviert werden). Wenn das gesamte Array aus dem missing_value_placeholder besteht, wird das Array mit constant_value oder 0 gefüllt, wenn nicht angegeben.  

**Syntax**

`series_fill_linear(`*x* `[,` *missing_value_placeholder*` [,`*fill_edges*` [,`*constant_value*`]]]))`
* Gibt die lineare Interpolation von *x* mit den angegebenen Parametern zurück.
 

**Argumente**

* *x*: dynamischer Array-Skalarausdruck, der ein Array numerischer Werte ist.
* *missing_value_placeholder*: optionaler Parameter, der einen Platzhalter für die zu ersetzenden "fehlenden Werte" angibt. Der Standardwert ist `double`(*null*).
* *fill_edges*: Boolescher Wert, der angibt, ob *missing_value_placeholder* am Anfang und am Ende des Arrays durch den nächsten Wert ersetzt werden soll. *Standardmäßig true.* Wenn auf *false* gesetzt, wird *missing_value_placeholder* am Anfang und Am Ende des Arrays beibehalten.
* *constant_value*: Optionaler Parameter, der nur für Arrays relevant ist, besteht vollständig aus *NULL-Werten,* die einen konstanten Wert angeben, mit dem die Reihe gefüllt werden soll. Der Standardwert ist *0*. Wenn Sie diesen `double`Parameter auf (*null*) setzen, werden *null* Werte effektiv dort belassen, wo sie sich befinden.

**Hinweise**

* Um Interpolationsfunktionen nach [make-Series](make-seriesoperator.md) anzuwenden, wird empfohlen, *NULL* als Standardwert anzugeben: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* Die *missing_value_placeholder* können von jedem Typ sein, der in tatsächliche Elementtypen konvertiert wird. Daher `double`haben entweder `long`(*null*), (*null*) oder `int`(*null*) die gleiche Bedeutung.
* Wenn *missing_value_placeholder* missing_value_placeholder `double`ist (*null*) (oder nur weggelassen, die die gleiche Bedeutung haben), dann kann ein Ergebnis *NULL-Werte* enthalten. Verwenden Sie andere Interpolationsfunktionen, um sie auszufüllen. Derzeit unterstützen nur [series_outliers()](series-outliersfunction.md) *NULL-Werte* in Eingabe-Arrays.
* Die Funktion behält den ursprünglichen Typ von Arrayelementen bei. Wenn *x* nur *int-* oder *long-Elemente* enthält, gibt die lineare Interpolation gerundete interpolierte Werte zurück, anstatt exakte Werte.

**Beispiel**

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

|Arr|without_args|with_edges|wo_edges|with_const|
|---|---|---|---|---|
|[null,111.0,null,36.0,41.0,null,null,16.0,61.0,33.0,null,null]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|[null,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,null,null]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|
|[null,111,null,36,41,null,null,16,61,33,null,null]|[111,111,73,36,41,32,24,16,61,33,33,33]|[111,111,73,36,41,32,24,16,61,33,33,33]|[null,111,73,36,41,32,24,16,61,33,null,null]|[111,111,74,38, 41,32,24,16,61,33,33,33]|
|[null,null,null,null,null]|[0.0,0.0,0.0,0.0]|[0.0,0.0,0.0,0.0]|[0.0,0.0,0.0,0.0]|[3.14159,3.14159,3.14159,3.14159]|