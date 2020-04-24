---
title: Als Operator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird als Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 05dc96fb7eec773d1e55d8b94a33cdda928622ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518427"
---
# <a name="as-operator"></a>as-Operator

Bindet einen Namen an den Eingabetabellenausdruck des Operators, sodass die Abfrage mehrmals auf den Wert des Tabellenausdrucks verweisen kann, ohne die Abfrage zu brechen und einen Namen durch die [let-Anweisung](letstatement.md)zu binden.

**Syntax**

*T* `|` T `as` `hint.materialized` [ `=` ] *Name* `true`

**Argumente**

* *T*: Ein tabellarischer Ausdruck.
* *Name*: Ein temporärer Name für den tabellenförmigen Ausdruck.
* `hint.materialized`: Wenn `true`auf gesetzt, wird der Wert des tabellarischen Ausdrucks so materialisiert, als ob er von einem [materialize()-Funktionsaufruf](./materializefunction.md) umschlossen wurde.

**Hinweise**

* Der angegebene `as` Name wird in `withsource=` der Spalte `source_` von [union](./unionoperator.md), `$table` der Spalte [find](./findoperator.md)und der Spalte [search](./searchoperator.md)verwendet.

* Der tabellarische Ausdruck, der mit dem Operator in`$left` [der](./joinoperator.md)äußeren tabellarischen Eingabe von ' ' benannt wird ( ) kann auch in der tabellarischen inneren Eingabe der Verknüpfung verwendet werden (`$right`).

**Beispiele**

```kusto
// 1. In the following 2 example the union's generated TableName column will consist of 'T1' and 'T2'
range x from 1 to 10 step 1 
| as T1 
| union withsource=TableName T2

union withsource=TableName (range x from 1 to 10 step 1 | as T1), T2

// 2. In the following example, the 'left side' of the join will be: 
//      MyLogTable filtered by type == "Event" and Name == "Start"
//    and the 'right side' of the join will be: 
//      MyLogTable filtered by type == "Event" and Name == "Stop"
MyLogTable  
| where type == "Event"
| as T
| where Name == "Start"
| join (
    T
    | where Name == "Stop"
) on ActivityId
```