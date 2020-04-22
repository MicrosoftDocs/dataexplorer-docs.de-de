---
title: tabelle() (Bereichsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird table() (Bereichsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 233baebaf51cdc8b07cdd32cec9bd10a54695c1c
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766154"
---
# <a name="table-scope-function"></a>tabelle() (Bereichsfunktion)

Die Table()-Funktion verweist auf eine Tabelle, `string`indem sie ihren Namen als Ausdruck vom Typ bereitstellt.

```kusto
table('StormEvent')
```

**Syntax**

`table``(` *Tabellenname* `,` [ *DataScope*]`)`

**Argumente**

::: zone pivot="azuredataexplorer"

* *TableName*: Ein `string` Ausdruck des Typs, der den Namen der Tabelle bereitstellt, auf die verwiesen wird. Der Wert dieses Ausdrucks muss am Zeitpunkt des Aufrufs der Funktion konstant sein (d. h. er kann nicht durch den Datenkontext variieren).

* *DataScope*: Ein optionaler Parameter vom Typ, `string` der verwendet werden kann, um den Tabellenverweis auf Daten zu beschränken, je nachdem, wie diese Daten unter die effektive [Cacherichtlinie](../management/cachepolicy.md)der Tabelle fallen. Wenn es verwendet wird, muss `string` das eigentliche Argument ein konstanter Ausdruck sein, der einen der folgenden möglichen Werte hat:

    - `"hotcache"`: Es wird nur auf Daten verwiesen, die als Hot-Cache kategorisiert sind.
    - `"all"`: Auf alle Daten in der Tabelle wird verwiesen.
    - `"default"`: Dies ist `"all"`dasselbe wie , es `"hotcache"` sei denn, der Cluster wurde vom Clusteradministrator als Standard festgelegt.

::: zone-end

::: zone pivot="azuremonitor"

* *TableName*: Ein `string` Ausdruck des Typs, der den Namen der Tabelle bereitstellt, auf die verwiesen wird. Der Wert dieses Ausdrucks muss am Zeitpunkt des Aufrufs der Funktion konstant sein (d. h. er kann nicht durch den Datenkontext variieren).

* *DataScope*: Ein optionaler Parameter vom Typ, `string` der verwendet werden kann, um den Tabellenverweis auf Daten zu beschränken, je nachdem, wie diese Daten unter die effektive Cacherichtlinie der Tabelle fallen. Wenn es verwendet wird, muss `string` das eigentliche Argument ein konstanter Ausdruck sein, der einen der folgenden möglichen Werte hat:

    - `"hotcache"`: Es wird nur auf Daten verwiesen, die als Hot-Cache kategorisiert sind.
    - `"all"`: Auf alle Daten in der Tabelle wird verwiesen.
    - `"default"`: Dies ist `"all"`das gleiche wie .

::: zone-end

## <a name="examples"></a>Beispiele

### <a name="use-table-to-access-table-of-the-current-database"></a>Verwenden von table() zum Zugriff auf die Tabelle der aktuellen Datenbank

```kusto
table('StormEvent') | count
```

|Anzahl|
|---|
|59066|

### <a name="use-table-inside-let-statements"></a>Verwenden von table() inside let-Anweisungen

Die gleiche Abfrage wie oben kann umgeschrieben werden, um die `tableName` Inline-Funktion (let-Anweisung) zu verwenden, die einen Parameter empfängt - der an die Table()-Funktion übergeben wird.

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

### <a name="use-table-inside-functions"></a>Verwenden von table() innerhalb von Funktionen

Die gleiche Abfrage wie oben kann umgeschrieben werden, um `tableName` in einer Funktion verwendet zu werden, die einen Parameter empfängt - der an die Table()-Funktion übergeben wird.

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

::: zone pivot="azuredataexplorer"

**Hinweis:** Solche Funktionen können nur lokal und nicht in der clusterübergreifenden Abfrage verwendet werden.

::: zone-end

### <a name="use-table-with-non-constant-parameter"></a>Verwenden sie table() mit nicht konstantem Parameter

Ein Parameter, der keine skalare Konstante zeichenfolge ist, `table()` kann nicht als Parameter für die Funktion übergeben werden.

Unten, ein Beispiel für die Problemumgehung für einen solchen Fall.

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
