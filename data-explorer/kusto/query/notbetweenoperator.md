---
title: notbetween Operator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt notBetween Operator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: eacde679f05ff79f5ee0d223ba005217dbf5192c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512086"
---
# <a name="between-operator"></a>!zwischen Operator

Entspricht der Eingabe, die sich außerhalb des inklusiven Bereichs befindet.

```kusto
Table1 | where Num1 !between (1 .. 10)
Table1 | where Time !between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`!between`kann für jeden numerischen, Datums- oder Zeitspanausdruck ausgeführt werden.
 
**Syntax**

*T* `|` T `where` *expr* `!between` *leftRange*` .. `*rightRange* leftRange rightRange `(``)`   
 
Wenn *expr-Ausdruck* datetime ist - wird eine andere syntaktische Zuckersyntax bereitgestellt:

*T* `|` T `where` *expr* `!between` *leftRangeDateTime*` .. `*rightRangeTimespan* leftRangeDateTime rechtsRangeTimespan `(``)`   

**Argumente**

* *T* - Die tabellarische Eingabe, deren Datensätze übereinstimmen sollen.
* *expr* - der zu filternde Ausdruck.
* *leftRange* - Ausdruck des linken Bereichs (inklusive).
* *rightRange* - Ausdruck des rihgt-Bereichs (inklusive).

**Rückgabe**

Zeilen in *T,* für die das Prädikat von (*expr* <  `true`*leftRange* oder *expr* > *rightRange*) ausgewertet wird, wird auf ausgewertet.

**Beispiele**  

**Filtern numerischer Werte mit dem Operator '!between'**  

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

**Filtern von Datumszeiten mithilfe des Operators 'between'**  


```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Anzahl|
|---|
|58590|


```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. 3d)
| count 
```

|Anzahl|
|---|
|58590|