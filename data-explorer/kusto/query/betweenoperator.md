---
title: zwischen Operator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt zwischen Operator enAzure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 336d24edbcd7574f0c4b6375b4b09014b38d10ec
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517849"
---
# <a name="between-operator"></a>between-Operator

Gleicht die Eingabe ab, die innerhalb des inklusiven Bereichs liegt.

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between`kann für jeden numerischen, Datums- oder Zeitspanausdruck ausgeführt werden.
 
**Syntax**

*T* `|` T `where` *expr* `between` *leftRange*` .. `*rightRange* leftRange rightRange `(``)`   
 
Wenn *expr-Ausdruck* datetime ist - wird eine andere syntaktische Zuckersyntax bereitgestellt:

*T* `|` T `where` *expr* `between` *leftRangeDateTime*` .. `*rightRangeTimespan* leftRangeDateTime rechtsRangeTimespan `(``)`   

**Argumente**

* *T* - Die tabellarische Eingabe, deren Datensätze übereinstimmen sollen.
* *expr* - der zu filternde Ausdruck.
* *leftRange* - Ausdruck des linken Bereichs (inklusive).
* *rightRange* - Ausdruck des rechten Bereichs (inklusive).

**Rückgabe**

Zeilen in *T,* für die das Prädikat von (*expr* >=  `true`*leftRange* und *expr* <= *rightRange*) ausgewertet wird, wird auf ausgewertet.

**Beispiele**  

**Filtern numerischer Werte mithilfe des Operators 'between'**  

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

**Filtern von Datumszeiten mithilfe des Operators 'between'**  


```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Anzahl|
|---|
|476|


```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count 
```

|Anzahl|
|---|
|476|