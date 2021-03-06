---
title: series_fill_const ()-Azure Daten-Explorer
description: In diesem Artikel wird series_fill_const () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d775b6029a7449912db16d5fc232c6eabd0996ff
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250876"
---
# <a name="series_fill_const"></a>series_fill_const()

Ersetzt fehlende Werte in einer Reihe durch einen angegebenen konstanten Wert.

Nimmt einen Ausdruck, der das dynamische numerische Array enthält, als Eingabe, ersetzt alle Instanzen missing_value_placeholder durch den angegebenen constant_value und gibt das resultierende Array zurück.

## <a name="syntax"></a>Syntax

`series_fill_const(`*x* `, ` *constant_value* `[,` *missing_value_placeholder*`])`
* Gibt Series *x* zurück, wobei alle Instanzen von *missing_value_placeholder* durch *constant_value*ersetzt werden.

## <a name="arguments"></a>Argumente

* *x*: Skalarausdruck des dynamischen Arrays, bei dem es sich um ein Array numerischer Werte handelt.
* *constant_value*: der-Wert, der fehlende Werte ersetzt. 
* *missing_value_placeholder*: optionaler Parameter, der einen Platzhalter für einen fehlenden zu ersetzenden Wert angibt. Der Standardwert ist `double` (*null*).

**Notizen**
* Wenn Sie die Reihe mithilfe des Operators " [make-Series](make-seriesoperator.md) " erstellen, werden die fehlenden Werte mit dem Standardwert 0 ausgefüllt. Sie können auch einen konstanten Wert angeben, der ausgefüllt werden soll, indem Sie `default = ` *DefaultValue* in der Make-Series-Anweisung angeben.

```kusto
make-series num=count() default=-1 on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* Geben Sie *null* als Standardwert an, um nach der [make-Reihe](make-seriesoperator.md)Interpolations Funktionen anzuwenden: 

```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* Der *missing_value_placeholder* kann einen beliebigen Typ aufweisen, der in tatsächliche Elementtypen konvertiert wird. Daher `double` haben entweder (*null*), `long` (*null*) oder `int` (*null*) dieselbe Bedeutung.
* Die-Funktion behält den ursprünglichen Typ der Array Elemente bei. 

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(`arr`: dynamic)
[
    dynamic([111,null,36,41,23,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_const1 = series_fill_const(arr, 0.0),
          fill_const2 = series_fill_const(arr, -1)  
```

|`arr`|`fill_const1`|`fill_const2`|
|---|---|---|
|[111, NULL, 36, 41, 23, NULL, 16, 61, 33, NULL, NULL]|[111, 0,0, 36, 41, 23, 0,0, 16, 61, 33, 0,0, 0,0]|[111,-1, 36, 41, 23,-1, 16, 61, 33,-1,-1]|
