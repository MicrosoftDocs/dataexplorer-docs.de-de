---
title: row_cumsum() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird row_cumsum() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 92ebec75dcd7e44d59f964dc735e857f22f1ad00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510216"
---
# <a name="row_cumsum"></a>row_cumsum()

Berechnet die kumulative Summe einer Spalte in einem [serialisierten Zeilensatz](./windowsfunctions.md#serialized-row-set).

**Syntax**

`row_cumsum``(` *Begriff* `,` [ *Neustart*]`)`

* *Begriff* ist ein Ausdruck, der den zu summierenden Wert angibt.
  Der Ausdruck muss ein Skalar eines der `decimal`folgenden `int` `long`Typen `real`sein: , , , oder . *Null-Term-Werte* wirken sich nicht auf die Summe aus.
* *Neustart* ist ein optionales Argument vom Typ, `bool` das angibt, wann der Akkumulationsvorgang neu gestartet werden soll (zurück auf 0). Es kann verwendet werden, um Partitionen der Daten anzugeben; siehe das zweite Beispiel unten.

**Rückgabe**

Die Funktion gibt die kumulative Summe ihres Arguments zurück.

**Beispiele**

Das folgende Beispiel zeigt, wie die kumulative Summe der ersten paar ebenen Zahlen berechnet wird.

```kusto
datatable (a:long) [
    1, 2, 3, 4, 5, 6, 7, 8, 9, 10
]
| where a%2==0
| serialize cs=row_cumsum(a)
```

a    | cs
-----|-----
2    | 2
4    | 6
6    | 12
8    | 20
10   | 30

Dieses Beispiel zeigt, wie die kumulative `salary`Summe (hier, von ) berechnet `name`wird, wenn die Daten partitioniert werden (hier nach):

```kusto
datatable (name:string, month:int, salary:long)
[
    "Alice", 1, 1000,
    "Bob",   1, 1000,
    "Alice", 2, 2000,
    "Bob",   2, 1950,
    "Alice", 3, 1400,
    "Bob",   3, 1450,
]
| order by name asc, month asc
| extend total=row_cumsum(salary, name != prev(name))
```

name   | month  | Gehalt  | total
-------|--------|---------|------
Alina  | 1      | 1000    | 1000
Alina  | 2      | 2000    | 3000
Alina  | 3      | 1400    | 4400
Bernd    | 1      | 1000    | 1000
Bernd    | 2      | 1950    | 2950
Bernd    | 3      | 1450    | 4400