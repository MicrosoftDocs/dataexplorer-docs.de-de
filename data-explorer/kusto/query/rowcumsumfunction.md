---
title: row_cumsum ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird row_cumsum () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ad1df20972238bee17217f5d9de19a020b4cbce
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242858"
---
# <a name="row_cumsum"></a>row_cumsum()

Berechnet die kumulierte Summe einer Spalte in einem [serialisierten Zeilen Satz](./windowsfunctions.md#serialized-row-set).

## <a name="syntax"></a>Syntax

`row_cumsum``(` *Begriff* [ `,` *Neustart*]`)`

* *Term* ist ein Ausdruck, der den zu summierenden Wert angibt.
  Der Ausdruck muss ein Skalar von einem der folgenden Typen sein: `decimal` , `int` , `long` oder `real` . NULL- *Begriffs* Werte wirken sich nicht auf die Summe aus.
* *Restart* ist ein optionales Argument vom Typ `bool` , das angibt, wann der Akkumulations Vorgang neu gestartet werden soll (auf 0 zurücksetzen). Sie kann verwendet werden, um Partitionen der Daten anzugeben. Weitere Informationen finden Sie im zweiten Beispiel weiter unten.

## <a name="returns"></a>Rückgabe

Die-Funktion gibt die kumulative Summe des Arguments zurück.

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird gezeigt, wie die kumulierte Summe der ersten geraden Ganzzahlen berechnet wird.

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

In diesem Beispiel wird gezeigt, wie die kumulierte Summe (hier von `salary` ) berechnet wird, wenn die Daten partitioniert werden (hier von `name` ):

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

name   | month  | End  | total
-------|--------|---------|------
Alina  | 1      | 1000    | 1000
Alina  | 2      | 2000    | 3000
Alina  | 3      | 1400    | 4400
Bernd    | 1      | 1000    | 1000
Bernd    | 2      | 1950    | 2950
Bob    | 3      | 1450    | 4400