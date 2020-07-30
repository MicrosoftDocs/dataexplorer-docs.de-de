---
title: Aufrufen von Operator-Azure Daten-Explorer
description: Dieser Artikel beschreibt den Aufruf Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: debd0a83f4c27fa05415805404ad450bab1d3ca1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347373"
---
# <a name="invoke-operator"></a>invoke-Operator

Ruft Lambda auf, das die Quelle von `invoke` als tabellarisches Parameter Argument empfängt.

```kusto
T | invoke foo(param1, param2)
```

## <a name="syntax"></a>Syntax

`T | invoke`- *Funktion* `(` [*param1* `,` *Param2*]`)`

## <a name="arguments"></a>Argumente

* *T*: die tabellarische Quelle.
* *Function*: der Name des Lambda-Ausdrucks oder Funktionsnamens, der ausgewertet werden soll.
* *param1*, *Param2* ...: zusätzliche Lambda-Argumente.

## <a name="returns"></a>Gibt zurück

Gibt das Ergebnis des ausgewerteten Ausdrucks zurück.

**Notizen**

Weitere Informationen zum Deklarieren von Lambda-Ausdrücken, die tabellarische Argumente annehmen können, finden [Sie unter Let-Anweisungen](./letstatement.md) .

## <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie der-Operator verwendet wird `invoke` , um einen Lambda Ausdruck aufzurufen:

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
```kusto
// clipped_average(): calculates percentiles limits, and then makes another 
//                    pass over the data to calculate average with values inside the percentiles
let clipped_average = (T:(x: long), lowPercentile:double, upPercentile:double)
{
   let high = toscalar(T | summarize percentiles(x, upPercentile));
   let low = toscalar(T | summarize percentiles(x, lowPercentile));
   T 
   | where x > low and x < high
   | summarize avg(x) 
};
range x from 1 to 100 step 1
| invoke clipped_average(5, 99)
```

|avg_x|
|---|
|52|
