---
title: series_fill_const ()-Azure Daten-Explorer
description: In diesem Artikel wird series_fill_const () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bb3f217b1ec0631f533a10433a7be368945667d7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87344534"
---
# <a name="series_fill_const"></a>series_fill_const()

Ersetzt fehlende Werte in einer Reihe durch einen angegebenen konstanten Wert.

Nimmt einen Ausdruck, der das dynamische numerische Array enthält, als Eingabe, ersetzt alle Instanzen von missing_value_placeholder durch angegebene constant_value und gibt das resultierende Array zurück.

## <a name="syntax"></a>Syntax

`series_fill_const(`*x* `[, ` *constant_value* `[,` *missing_value_placeholder*`]])`
* Gibt Series *x* zurück, wobei alle Instanzen von *missing_value_placeholder* durch *constant_value*ersetzt werden.

## <a name="arguments"></a>Argumente

* *x*: Skalarausdruck des dynamischen Arrays, bei dem es sich um ein Array numerischer Werte handelt.
* *constant_value*: Parameter, der einen Platzhalter für einen fehlenden zu ersetzenden Wert angibt. Der Standardwert ist *0*. 
* *missing_value_placeholder*: optionaler Parameter, der einen Platzhalter für einen fehlenden zu ersetzenden Wert angibt. Der Standardwert ist `double` (*null*).

**Hinweise**
* Sie können eine Reihe erstellen, die mit einem konstanten Wert ausgefüllt wird, indem Sie `default = ` die *DefaultValue* -Syntax verwenden (oder nur weglassen, der die Annahme 0 annimmt). Weitere Informationen finden Sie unter [make-Series](make-seriesoperator.md).

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
