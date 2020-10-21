---
title: 'Table () (Scope-Funktion): Azure Daten-Explorer'
description: In diesem Artikel wird die Tabelle () (Scope-Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 80035e20110b8a1f2fb705ef73f75275f60fcdda
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251307"
---
# <a name="table-scope-function"></a>Table () (Scope-Funktion)

Die Table ()-Funktion verweist auf eine Tabelle, indem Sie Ihren Namen als Ausdruck vom Typ bereitstellt `string` .

```kusto
table('StormEvent')
```

## <a name="syntax"></a>Syntax

`table``(` *TableName* [ `,` *Datascope*]`)`

## <a name="arguments"></a>Argumente

::: zone pivot="azuredataexplorer"

* *TableName*: ein Ausdruck vom Typ `string` , der den Namen der Tabelle bereitstellt, auf die verwiesen wird. Der Wert dieses Ausdrucks muss an dem Punkt, an dem die Funktion aufgerufen wird, konstant sein (d. h., er kann nicht vom Datenkontext abweichen).

* *Datascope*: ein optionaler Parameter vom Typ `string` , der verwendet werden kann, um den Tabellen Verweis auf Daten zu beschränken, wie diese Daten unter die effektive [Cache Richtlinie](../management/cachepolicy.md)der Tabelle fallen. Wenn Sie verwendet wird, muss das tatsächliche Argument ein konstanter Ausdruck sein, der `string` einen der folgenden möglichen Werte hat:

    - `"hotcache"`: Es wird nur auf Daten verwiesen, die als "Hot Cache" kategorisiert sind.
    - `"all"`: Auf alle Daten in der Tabelle wird verwiesen.
    - `"default"`: Dies ist identisch mit `"all"` , es sei denn, der Cluster wurde so festgelegt, dass er `"hotcache"` vom Cluster Administrator als Standard verwendet wird.

::: zone-end

::: zone pivot="azuremonitor"

* *TableName*: ein Ausdruck vom Typ `string` , der den Namen der Tabelle bereitstellt, auf die verwiesen wird. Der Wert dieses Ausdrucks muss an dem Punkt, an dem die Funktion aufgerufen wird, konstant sein (d. h., er kann nicht vom Datenkontext abweichen).

* *Datascope*: ein optionaler Parameter vom Typ `string` , der verwendet werden kann, um den Tabellen Verweis auf Daten zu beschränken, wie diese Daten unter die effektive Cache Richtlinie der Tabelle fallen. Wenn Sie verwendet wird, muss das tatsächliche Argument ein konstanter Ausdruck sein, der `string` einen der folgenden möglichen Werte hat:

    - `"hotcache"`: Es wird nur auf Daten verwiesen, die als "Hot Cache" kategorisiert sind.
    - `"all"`: Auf alle Daten in der Tabelle wird verwiesen.
    - `"default"`: Entspricht `"all"` .

::: zone-end

## <a name="examples"></a>Beispiele

### <a name="use-table-to-access-table-of-the-current-database"></a>Verwenden Sie Table (), um auf die Tabelle der aktuellen Datenbank zuzugreifen.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
table('StormEvent') | count
```

|Anzahl|
|---|
|59066|

### <a name="use-table-inside-let-statements"></a>Verwenden von Table () in Let-Anweisungen

Dieselbe Abfrage wie oben kann so umgeschrieben werden, dass Sie eine Inline Funktion (Let-Anweisung) verwendet, die einen Parameter empfängt, `tableName` der an die Table ()-Funktion übergeben wird.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo = (tableName:string)
{
    table(tableName) | count
};
foo('help')
```

|Anzahl|
|---|
|59066|

### <a name="use-table-inside-functions"></a>Verwenden von Table () innerhalb von Funktionen

Dieselbe Abfrage wie oben kann so umgeschrieben werden, dass Sie in einer Funktion verwendet wird, die einen Parameter empfängt, der an `tableName` die Table ()-Funktion übergeben wird.

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

::: zone pivot="azuredataexplorer"

**Hinweis:** solche Funktionen können nur lokal und nicht in der Cluster übergreifenden Abfrage verwendet werden.

::: zone-end

### <a name="use-table-with-non-constant-parameter"></a>Verwenden von Table () mit einem nicht konstanten Parameter

Ein Parameter, der keine skalare Konstante Zeichenfolge ist, kann nicht als Parameter an die Funktion übergeben werden `table()` .

Im folgenden finden Sie ein Beispiel für eine Problem Umgehung für diesen Fall.

```kusto
let T1 = print x=1;
let T2 = print x=2;
let _choose = (_selector:string)
{
    union
    (T1 | where _selector == 'T1'),
    (T2 | where _selector == 'T2')
};
_choose('T2')

```

|x|
|---|
|2|
