---
title: zip ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird zip () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 28fc477d4dfc5432434261e493f36985514ea28b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350552"
---
# <a name="zip"></a>zip()

Die `zip` Funktion nimmt eine beliebige Anzahl von `dynamic` Arrays an und gibt ein Array zurück, dessen Elemente jeweils ein Array sind, das die Elemente der Eingabe Arrays desselben Indexes enthält.

## <a name="syntax"></a>Syntax

`zip(`*array1* `,` *array2*`, ... )`

## <a name="arguments"></a>Argumente

Zwischen 2 und 16 dynamischen Arrays.

## <a name="examples"></a>Beispiele

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