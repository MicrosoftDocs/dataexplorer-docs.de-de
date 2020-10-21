---
title: zip ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird zip () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33b914babb8c197f997326cd6dd44654c05d1d9d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253135"
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