---
title: series_fill_backward ()-Azure Daten-Explorer
description: In diesem Artikel wird series_fill_backward () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bc26c61b9a94c6f21d2c53cae8fc80805b235f75
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372819"
---
# <a name="series_fill_backward"></a>series_fill_backward()

Führt eine rückwärts Füll interpolung von fehlenden Werten in einer Reihe aus.

Ein Ausdruck, der das dynamische numerische Array enthält, ist die Eingabe. Die-Funktion ersetzt alle Instanzen von missing_value_placeholder durch den nächstgelegenen Wert von der rechten Seite (außer missing_value_placeholder) und gibt das resultierende Array zurück. Die am weitesten rechts stehenden Instanzen von missing_value_placeholder werden beibehalten.

**Syntax**

`series_fill_backward(`*x* `[, ` *missing_value_placeholder*`])`
* Gibt Series *x* zurück, wobei alle Instanzen von *missing_value_placeholder* rückwärts ausgefüllt werden.

**Argumente**

* *x*: Skalarausdruck des dynamischen Arrays, bei dem es sich um ein Array numerischer Werte handelt.
* *missing_value_placeholder*: mit diesem optionalen Parameter wird ein Platzhalter für fehlende Werte angegeben. Der Standardwert ist `double` (*null*).

**Hinweise**

* Geben Sie *null* als Standardwert an, um alle Interpolations Funktionen nach der [make-Serie](make-seriesoperator.md)anzuwenden: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* Der *missing_value_placeholder* kann einen beliebigen Typ aufweisen, der in tatsächliche Elementtypen konvertiert wird. Beide `double` (*null*), `long` (*null*) und `int` (*null*) haben dieselbe Bedeutung.
* Wenn *missing_value_placeholder* `double` (*null*) ist (oder weggelassen wird, das die gleiche Bedeutung hat), kann ein Ergebnis *null* -Werte enthalten. Um diese *null* -Werte auszufüllen, verwenden Sie andere Interpolations Funktionen. Derzeit unterstützen nur [series_outliers ()](series-outliersfunction.md) *null* -Werte in Eingabe Arrays.
* Die-Funktion behält den ursprünglichen Typ von Array Elementen bei.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_backward(arr)

```

|`arr`|`fill_forward`|
|---|---|
|[111, NULL, 36, 41, NULL, NULL, 16, 61, 33, NULL, NULL]|[111, 36, 36, 41, 16, 16, 16, 61, 33, NULL, NULL]|

  
Verwenden Sie [series_fill_forward](series-fill-forwardfunction.md) oder [Series-Fill-Konstanten](series-fill-constfunction.md) , um die interpolung des obigen Arrays abzuschließen.
