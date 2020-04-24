---
title: series_fill_const() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden series_fill_const() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c7f5f939068737bdd6519fc0c63b663d19ae076a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508771"
---
# <a name="series_fill_const"></a>series_fill_const()

Ersetzt fehlende Werte in einer Reihe durch einen angegebenen konstanten Wert.

Nimmt einen Ausdruck, der dynamisches numerisches Array als Eingabe enthält, ersetzt alle Instanzen von missing_value_placeholder durch angegebene constant_value und gibt das resultierende Array zurück.

**Syntax**

`series_fill_const(`*x*`[, `*constant_value* `[,` *missing_value_placeholder*`]])`
* Gibt die Serie *x* zurück, wobei alle Instanzen von *missing_value_placeholder* durch *constant_value*ersetzt werden.

**Argumente**

* *x*: dynamischer Array-Skalarausdruck, der ein Array numerischer Werte ist.
* *constant_value*: Parameter, der einen Platzhalter für fehlende Werte angibt, die ersetzt werden sollen. Der Standardwert ist *0*. 
* *missing_value_placeholder*: optionaler Parameter, der einen Platzhalter für fehlende Werte angibt, die ersetzt werden sollen. Der Standardwert ist `double`(*null*).

**Hinweise**
* Es ist möglich, eine Serie mit konstanter Ausfüllung eines Aufrufs mit `default = ` *DefaultValue-Syntax* zu erstellen (oder einfach nur das Auslassen, das 0 annimmt). Weitere Informationen finden Sie unter [serien.](make-seriesoperator.md)

```kusto
make-series num=count() default=-1 on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* Um Interpolationsfunktionen nach [make-Series](make-seriesoperator.md) anzuwenden, wird empfohlen, *NULL* als Standardwert anzugeben: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* Die *missing_value_placeholder* können von jedem Typ sein, der in tatsächliche Elementtypen konvertiert wird. Daher `double`haben entweder `long`(*null*), (*null*) oder `int`(*null*) die gleiche Bedeutung.
* Die Funktion behält den ursprünglichen Typ der Arrayelemente bei. 

**Beispiel**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,23,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_const1 = series_fill_const(arr, 0.0),
          fill_const2 = series_fill_const(arr, -1)  
```

|Arr|fill_const1|fill_const2|
|---|---|---|
|[111,null,36,41,23,null,16,61,33,null,null]|[111,0.0,36,41,23,0.0,16,61,33,0.0,0.0]|[111,-1,36,41,23,-1,16,61,33,-1,-1]|