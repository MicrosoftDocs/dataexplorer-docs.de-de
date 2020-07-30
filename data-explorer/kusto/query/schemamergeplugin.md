---
title: 'schema_merge-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird schema_merge-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 2873f3d010464b82ef8cb6a9a3e09f7b0a56b8d9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345707"
---
# <a name="schema_merge-plugin"></a>Plug-In „schema_merge“

Führt tabellarische Schema Definitionen in einem vereinheitlichten Schema zusammen. 

Schema Definitionen werden in dem vom Operator erzeugten Format erwartet [`getschema`](./getschemaoperator.md) .

Der `schema merge` Vorgang verbindet Spalten in Eingabe Schemas und versucht, die Datentypen auf allgemeine Datentypen zu reduzieren. Wenn die Datentypen nicht reduziert werden können, wird ein Fehler in der problematischen Spalte angezeigt.

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

## <a name="syntax"></a>Syntax

`T``|` `evaluate` `schema_merge(` *Preserveorder*`)`

## <a name="arguments"></a>Argumente

* *Preserveorder*: (optional) Wenn Sie auf festgelegt `true` ist, weist das Plug-in an, die Spaltenreihenfolge zu validieren, wie vom ersten tabellarischen Schema definiert. Wenn dieselbe Spalte in mehreren Schemas enthalten ist, muss die Spalten Ordnungszahl wie die Spalten Ordnungszahl des ersten Schemas lauten, in dem Sie enthalten war. Der Standardwert ist `true`sein.

## <a name="returns"></a>Rückgabe

Das `schema_merge` Plug-in gibt eine Ausgabe ähnlich der [`getschema`](./getschemaoperator.md) Rückgabe des Operators zurück.

## <a name="examples"></a>Beispiele

Zusammenführen mit einem Schema, dem eine neue Spalte angefügt wurde.

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, HttpStatus:int, Referrer:string)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*Ergebnis*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|Zeichenfolge|
|HttpStatus|1|System. Int32|INT|
|Referrer|2|System.String|Zeichenfolge|

Führen Sie eine Zusammenführung mit einem Schema aus, das eine andere Spalten Sortierung aufweist ( `HttpStatus` ordinaländerungen von in `1` `2` in der neuen Variante).

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*Ergebnis*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|Zeichenfolge|
|Referrer|1|System.String|Zeichenfolge|
|HttpStatus|-1|Fehler (Unbekannter CSL-Typ: Fehler (Spalten sind nicht in der richtigen Reihenfolge))|Fehler (Spalten sind nicht in der richtigen Reihenfolge)|

Führen Sie eine Zusammenführung mit einem Schema aus, das eine andere Spalten Reihenfolge aufweist `PreserveOrder` `false`

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge(PreserveOrder = false)
```

*Ergebnis*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|Zeichenfolge
|Referrer|1|System.String|Zeichenfolge
|HttpStatus|2|System. Int32|INT|
