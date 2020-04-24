---
title: series_fill_backward() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_fill_backward() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b8c3bb09c7067112fd358c94fd46ca85bea31358
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508737"
---
# <a name="series_fill_backward"></a>series_fill_backward()

Führt die Rückwärtsfüllinterpolation fehlender Werte in einer Reihe aus.

Nimmt einen Ausdruck, der dynamisches numerisches Array enthält, als Eingabe, ersetzt alle Instanzen von missing_value_placeholder durch den nächsten Wert von der rechten Seite außer missing_value_placeholder und gibt das resultierende Array zurück. Die rechtsten Instanzen von missing_value_placeholder werden beibehalten.

**Syntax**

`series_fill_backward(`*x*`[, `*missing_value_placeholder*`])`
* Gibt die Serie *x* mit allen Instanzen von *missing_value_placeholder* rückwärts gefüllt.

**Argumente**

* *x*: dynamischer Array-Skalarausdruck, der ein Array numerischer Werte ist.
* *missing_value_placeholder*: optionaler Parameter, der einen Platzhalter für fehlende Werte angibt, die ersetzt werden sollen. Der Standardwert ist `double`(*null*).

**Hinweise**

* Um Interpolationsfunktionen nach [make-Series](make-seriesoperator.md) anzuwenden, wird empfohlen, *NULL* als Standardwert anzugeben: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* Die *missing_value_placeholder* können von jedem Typ sein, der in tatsächliche Elementtypen konvertiert wird. Daher `double`haben entweder `long`(*null*), (*null*) oder `int`(*null*) die gleiche Bedeutung.
* Wenn *missing_value_placeholder* missing_value_placeholder `double`ist (*null*) (oder nur weggelassen, die die gleiche Bedeutung haben), dann kann ein Ergebnis *NULL-Werte* enthalten. Verwenden Sie andere Interpolationsfunktionen, um sie auszufüllen. Derzeit unterstützen nur [series_outliers()](series-outliersfunction.md) *NULL-Werte* in Eingabe-Arrays.
* Die Funktionen behalten den ursprünglichen Typ von Arrayelementen bei.

**Beispiel**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_backward(arr)

```

|Arr|fill_forward|
|---|---|
|[111,null,36,41,null,null,16,61,33,null,null]|[111,36,36,41,16, 16,16,61,33,null,null]|

  
Man kann [series_fill_forward](series-fill-forwardfunction.md) oder [series-fill-const](series-fill-constfunction.md) verwenden, um die Interpolation des obigen Arrays abzuschließen.