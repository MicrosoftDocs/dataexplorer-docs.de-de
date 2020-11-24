---
title: zwischen Operator-Azure Daten-Explorer
description: In diesem Artikel wird der Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: 8bb2049c7bc7c81092eb137c820f650bf88abc4e
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512977"
---
# <a name="between-operator"></a>between-Operator

Gleicht die Eingabe ab, die innerhalb des inklusiven Bereichs liegt.

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between` kann für jeden numerischen, DateTime-oder TimeSpan-Ausdruck verwendet werden.
 
## <a name="syntax"></a>Syntax

*T* `|` `where` *expr* `between` `(` *leftrange* ` .. ` *rightrange*`)`   
 
Wenn der *expr* -Ausdruck "DateTime" ist, wird eine andere syntaktische Sugar-Syntax bereitgestellt:

*T* `|` `where` *expr* `between` `(` *leftrangedatetime* ` .. ` *rightrangetimespan*`)`   

## <a name="arguments"></a>Argumente

* *T* : die tabellarische Eingabe, deren Datensätze abgeglichen werden sollen.
* *expr* -der Ausdruck, der gefiltert werden soll.
* *leftrange* -Ausdruck des linken Bereichs (inklusiv).
* *rightrange* -Ausdruck des rechten Bereichs (inklusiv).

## <a name="returns"></a>Gibt zurück

Zeilen in *T* , für die das Prädikat von (*expr*  >=  *leftrange* und *expr*  <=  *rightrange*) als ausgewertet wird `true` .

## <a name="examples"></a>Beispiele  

**Filtern numerischer Werte mithilfe eines ' between '-Operators**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 100 step 1
| where x between (50 .. 55)
```

|x|
|---|
|50|
|51|
|52|
|53|
|54|
|55|

**Filtern von DateTime mit einem ' between '-Operator**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Anzahl|
|---|
|476|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count 
```

|Anzahl|
|---|
|476|
