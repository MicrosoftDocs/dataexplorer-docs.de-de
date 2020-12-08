---
title: 'between-Operator: Azure Data Explorer'
description: In diesem Artikel wird der between-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: 8bb2049c7bc7c81092eb137c820f650bf88abc4e
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
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

*T* `|` `where` *expr* `between` `(`*leftRange*` .. `*rightRange*`)`   
 
Wenn der *expr*-Ausdruck DateTime ist, wird eine andere syntaktische Sugar-Syntax bereitgestellt:

*T* `|` `where` *expr* `between` `(`*leftRangeDateTime*` .. `*rightRangeTimespan*`)`   

## <a name="arguments"></a>Argumente

* *T*: Die tabellarische Eingabe, deren Datensätze abgeglichen werden sollen.
* *expr*: Der zu filternde Ausdruck.
* *leftRange*: Ausdruck des linken Bereichs (inklusiv).
* *rightRange*: Ausdruck des rechten Bereichs (inklusiv).

## <a name="returns"></a>Gibt zurück

Zeilen in *T*, für die das Prädikat (*expr* >= *leftRange* und *expr* <= *rightRange*) in `true`ausgewertet wird.

## <a name="examples"></a>Beispiele  

**Filtern numerischer Werte mithilfe des between-Operators**  

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

**Filtern von DateTime mit einem between-Operator**  

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
