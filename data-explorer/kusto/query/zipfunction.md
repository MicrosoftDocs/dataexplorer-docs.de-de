---
title: zip() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt zip() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd407ce652d41471be5b30a15c2c09b9f608edb1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504232"
---
# <a name="zip"></a>zip()

Die `zip` Funktion akzeptiert `dynamic` eine beliebige Anzahl von Arrays und gibt ein Array zurück, dessen Elemente jeweils ein Array sind, das die Elemente der Eingabearrays desselben Indexes enthält.

**Syntax**

`zip(`*array1*`,` *Array1-Array2*`, ... )`

**Argumente**

Zwischen 2 und 16 dynamischen Arrays.

**Beispiele**

Das folgende Beispiel gibt `[[1,2],[3,4],[5,6]]`zurück:

```kusto
print zip(dynamic([1,3,5]), dynamic([2,4,6]))
```

Das folgende Beispiel gibt `[["A",{}], [1,"B"], [1.5, null]]`zurück:

```kusto
print zip(dynamic(["A", 1, 1.5]), dynamic([{}, "B"]))
```

Das folgende Beispiel gibt `[[1,"one"],[2,"two"],[3,"three"]]`zurück:

```kusto
datatable(a:int, b:string) [1,"one",2,"two",3,"three"]
| summarize a = make_list(a), b = make_list(b)
| project zip(a, b)
```