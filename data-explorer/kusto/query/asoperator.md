---
title: 'as-Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird als Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 15dcf79938f4b83f18055b6f59a9b70998ab6049
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252811"
---
# <a name="as-operator"></a>as-Operator

Bindet einen Namen an den tabellarischen Eingabe Ausdruck des Operators, sodass die Abfrage mehrmals auf den Wert des tabellarischen Ausdrucks verweisen kann, ohne die Abfrage zu unterbrechen und einen Namen über die [Let-Anweisung](letstatement.md)zu binden.

## <a name="syntax"></a>Syntax

*T* `|` `as` [ `hint.materialized` `=` `true` ] *Name*

## <a name="arguments"></a>Argumente

* *T*: ein tabellarischer Ausdruck.
* *Name*: ein temporärer Name für den tabellarischen Ausdruck.
* `hint.materialized`: Wenn der Wert auf festgelegt `true` ist, wird der Wert des tabellarischen Ausdrucks materialisiert, als ob er von einem [Materialize ()](./materializefunction.md) -Funktions aufruten umschließt würde.

> [!NOTE]
> * Der von angegebene Name `as` wird in der- `withsource=` Spalte der [Union](./unionoperator.md), der `source_` Spalte von [Find](./findoperator.md)und der `$table` Spalte von [Search](./searchoperator.md)verwendet.
> * Der tabellarische Ausdruck, der mit [dem-Operator in der äußeren](./joinoperator.md)Tabellen Eingabe () eines Joins benannt wird, `$left` kann auch in der tabellarischen inneren Eingabe () des Joins verwendet werden `$right` .

## <a name="examples"></a>Beispiele

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