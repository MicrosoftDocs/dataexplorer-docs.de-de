---
title: Aufrufoperator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Aufrufoperator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41f19440795f4f302352a8dda5192c5c4790ea99
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513701"
---
# <a name="invoke-operator"></a>invoke-Operator

Ruft Lambda auf, das `invoke` die Quelle des Tabellenparameterarguments empfängt.

```kusto
T | invoke foo(param1, param2)
```

**Syntax**

`T | invoke`*Funktion*`(`[*param1* `,` *param2*]`)`

**Argumente**

* *T*: Die tabellarische Quelle.
* *Funktion*: Der Name des Lambda-Ausdrucks oder Funktionsnamens, der ausgewertet werden soll.
* *param1*, *param2* ... : zusätzliche Lambda-Argumente.

**Rückgabe**

Gibt das Ergebnis des ausgewerteten Ausdrucks zurück.

**Hinweise**

Weitere Informationen zum Deklarieren von Lambda-Ausdrücken, die tabellarische Argumente akzeptieren können, finden Sie unter [Let-Anweisungen.](./letstatement.md)

**Beispiel**

Das folgende Beispiel zeigt, wie der Operator zum Aufrufen des Lambda-Ausdrucks verwendet `invoke` wird:

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
