---
title: 'notBetween-Operator: Azure-Daten-Explorer'
description: In diesem Artikel wird der Operator "notBetween" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7dd8f04010cbc10afa34fa8b27e6be8d31feae9d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248698"
---
# <a name="not-between-operator-between"></a>not-between-Operator (!between)

Entspricht der Eingabe, die außerhalb des inklusiven Bereichs liegt.

```kusto
Table1 | where Num1 !between (1 .. 10)
Table1 | where Time !between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`!between` kann für jeden numerischen, DateTime-oder TimeSpan-Ausdruck verwendet werden.
 
## <a name="syntax"></a>Syntax

*T* `|` `where` *expr* `!between` `(` *leftrange* ` .. ` *rightrange*`)`   
 
Wenn der *expr* -Ausdruck "DateTime" ist, wird eine andere syntaktische Sugar-Syntax bereitgestellt:

*T* `|` `where` *expr* `!between` `(` *leftrangedatetime* ` .. ` *rightrangetimespan*`)`   

## <a name="arguments"></a>Argumente

* *T* : die tabellarische Eingabe, deren Datensätze abgeglichen werden sollen.
* *expr* -der Ausdruck, der gefiltert werden soll.
* *leftrange* -Ausdruck des linken Bereichs (inklusiv).
* *rightrange* -Ausdruck des rechten Bereichs (inklusiv).

## <a name="returns"></a>Rückgabe

Zeilen in *T* , für die das Prädikat von (*expr*  <  *leftrange* oder *expr*  >  *rightrange*) als ausgewertet wird `true` .

## <a name="examples"></a>Beispiele  

**Filtern numerischer Werte mithilfe des Operators '! between '**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 10 step 1
| where x !between (5 .. 9)
```

|x|
|---|
|1|
|2|
|3|
|4|
|10|

**Filtern von DateTime mit einem ' between '-Operator**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Anzahl|
|---|
|58590|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. 3d)
| count 
```

|Anzahl|
|---|
|58590|
