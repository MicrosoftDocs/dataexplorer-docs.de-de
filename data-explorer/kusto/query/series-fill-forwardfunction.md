---
title: series_fill_forward() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_fill_forward() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 6c79733aa1abf001f52eb07606c866904e370906
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508669"
---
# <a name="series_fill_forward"></a>series_fill_forward()

Führt die Vorwärtsfüllinterpolation fehlender Werte in einer Reihe durch.

Nimmt einen Ausdruck, der dynamisches numerisches Array enthält, als Eingabe, ersetzt alle Instanzen von missing_value_placeholder durch den nächsten Wert von der linken Seite außer missing_value_placeholder und gibt das resultierende Array zurück. Die linksgenannten Instanzen von missing_value_placeholder werden beibehalten.

**Syntax**

`series_fill_forward(`*x*`[, `*missing_value_placeholder*`])`
* Gibt die Serie *x* mit allen Instanzen von *missing_value_placeholder* nach vorne gefüllt.

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
    dynamic([null,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_forward(arr)  

```

|Arr|fill_forward|
|---|---|
|[null,null,36,41,null,null,16,61,33,null,null]|[null,null,36,41,41,41,16,61,33,33,33]|
   
Man kann [series_fill_backward](series-fill-backwardfunction.md) oder [series-fill-const](series-fill-constfunction.md) verwenden, um die Interpolation des obigen Arrays abzuschließen.