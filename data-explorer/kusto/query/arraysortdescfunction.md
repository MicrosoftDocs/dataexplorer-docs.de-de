---
title: array_sort_desc ()-Azure Daten-Explorer
description: In diesem Artikel wird array_sort_desc () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 09/22/2020
ms.openlocfilehash: 83be80a7eb440e73fd19f213839cef7d58ec1512
ms.sourcegitcommit: 62476f682b7812cd9cff7e6958ace5636ee46755
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169634"
---
# <a name="array_sort_desc"></a>array_sort_desc ()

Empfängt ein oder mehrere Arrays. Sortiert das erste Array in absteigender Reihenfolge. Ordnet die verbleibenden Arrays entsprechend dem neu bestellten ersten Array zu.

## <a name="syntax"></a>Syntax

`array_sort_desc(`*array1*[,..., *argumentn*]`)`

`array_sort_desc(`*array1*[,..., *argumentn*] `,` *nulls_last*`)`

Wenn *nulls_last* nicht angegeben wird, wird der Standardwert `true` verwendet.

## <a name="arguments"></a>Argumente

* *array1... arrayn*: Eingabe Arrays.
* *nulls_last*: ein boolescher Wert, der angibt, ob `null` s den letzten

## <a name="returns"></a>Gibt zurück

Gibt die gleiche Anzahl von Arrays wie in der Eingabe zurück, wobei das erste Array in aufsteigender Reihenfolge sortiert wird und die verbleibenden Arrays entsprechend dem neu bestellten ersten Array sortiert werden.

`null` wird für jedes Array zurückgegeben, das sich von der ersten unterscheidet.

Wenn ein Array Elemente verschiedener Typen enthält, wird es in der folgenden Reihenfolge sortiert:

* Numerische `datetime` -,-und- `timespan` Elemente
* Zeichen folgen Elemente
* GUID-Elemente
* Alle anderen Elemente

## <a name="example-1---sorting-two-arrays"></a>Beispiel 1: Sortieren von zwei Arrays

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let array1 = dynamic([1,3,4,5,2]);
let array2 = dynamic(["a","b","c","d","e"]);
print array_sort_desc(array1,array2)
```

|`array1_sorted`|`array2_sorted`|
|---|---|
|[5, 4, 3, 2, 1]|["d", "c", "b", "e", "a"]|

> [!Note]
> Die Namen der Ausgabespalten werden basierend auf den Argumenten der Funktion automatisch generiert. Verwenden Sie die folgende Syntax, um den Ausgabespalten unterschiedliche Namen zuzuweisen: `... | extend (out1, out2) = array_sort_desc(array1,array2)`

## <a name="example-2---sorting-substrings"></a>Beispiel 2: Sortieren von Teil Zeichenfolgen

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Names = "John,Paul,George,Ringo";
let SortedNames = strcat_array(array_sort_desc(split(Names, ",")), ",");
print result = SortedNames
```

|`result`|
|---|
|Ringo, Paul, John, George|

## <a name="example-3---combining-summarize-and-array_sort_desc"></a>Beispiel 3: Kombinieren von zusammenfassen und array_sort_desc

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable(command:string, command_time:datetime, user_id:string)
[
    'chmod',   datetime(2019-07-15),   "user1",
    'ls',      datetime(2019-07-02),   "user1",
    'dir',     datetime(2019-07-22),   "user1",
    'mkdir',   datetime(2019-07-14),   "user1",
    'rm',      datetime(2019-07-27),   "user1",
    'pwd',     datetime(2019-07-25),   "user1",
    'rm',      datetime(2019-07-23),   "user2",
    'pwd',     datetime(2019-07-25),   "user2",
]
| summarize timestamps = make_list(command_time), commands = make_list(command) by user_id
| project user_id, commands_in_chronological_order = array_sort_desc(timestamps, commands)[1]
```

|`user_id`|`commands_in_chronological_order`|
|---|---|
|user1|[<br>  "RM",<br>  "pwd",<br>  "dir",<br>  "chmod",<br>  "mkdir",<br>  ls<br>]|
|user2|[<br>  "pwd",<br>  RMS<br>]|

> [!Note]
> Wenn Ihre Datenwerte enthalten können `null` , verwenden Sie [make_list_with_nulls](make-list-with-nulls-aggfunction.md) anstelle von [make_list](makelist-aggfunction.md).

## <a name="example-4---controlling-location-of-null-values"></a>Beispiel 4: Steuern der Position von `null` Werten

Standardmäßig `null` werden Werte in das sortierte Array eingefügt. Sie können es jedoch explizit steuern, indem Sie einen `bool` Wert als letztes Argument hinzufügen `array_sort_desc()` .

Beispiel mit Standardverhalten:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print array_sort_desc(dynamic([null,"blue","yellow","green",null]))
```

|`print_0`|
|---|
|["gelb", "grün", "blau", NULL, NULL]|

Beispiel mit nicht standardmäßigen Verhalten:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print array_sort_desc(dynamic([null,"blue","yellow","green",null]), false)
```

|`print_0`|
|---|
|[NULL, NULL, "gelb", "grün", "blau"]|

## <a name="see-also"></a>Siehe auch

Um das erste Array in aufsteigender Reihenfolge zu sortieren, verwenden Sie [array_sort_asc ()](arraysortascfunction.md).
