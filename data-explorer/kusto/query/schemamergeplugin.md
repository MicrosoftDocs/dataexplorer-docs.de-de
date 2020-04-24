---
title: schema_merge-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird schema_merge-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 67326a0e7a92d064613ee3a3de2851addb502fc9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509247"
---
# <a name="schema_merge-plugin"></a>schema_merge Plugin

Führt tabellarische Schemadefinitionen in ein einheitliches Schema zusammen. 

Schemadefinitionen werden voraussichtlich im Format sein, wie sie vom [getschema-Operator](./getschemaoperator.md) erstellt wurden.

Der Schemazusammenführungsvorgang verbindet Spalten, die in Eingabeschemas angezeigt werden, und versucht, Datentypen auf einen gemeinsamen Datentyp zu reduzieren (falls Datentypen nicht reduziert werden können, wird ein Fehler in einer problematischen Spalte angezeigt).

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

**Syntax**

`T``|` `evaluate` *PreserveOrder* PreserveOrder `schema_merge(``)`

**Argumente**

* *PreserveOrder*: (Optional) `true`Wenn auf festgelegt, wird an das Plugin geleitet, um die Spaltenreihenfolge zu überprüfen, wie im ersten tabellarischen Schema definiert. Mit anderen Worten, wenn dieselbe Spalte in mehreren Schemas angezeigt wird, muss die Spaltenordinal wie im ersten Schema sein, das sie angezeigt hat. Der Standardwert ist `true`sein.

**Rückgabe**

Das `schema_merge` Plugin gibt Ausgabe-Simiar zurück, was [getschema-Operator](./getschemaoperator.md) zurückgibt.

**Beispiele**

Zusammenführen mit einem Schema, an das eine neue Spalte angehängt wurde:

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

Zusammenführen mit einem Schema mit`HttpStatus` einer anderen Spaltenreihenfolge (Ordinale Änderungen von `1` zu `2` in der neuen Variante):

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
|HttpStatus|-1|ERROR(unbekannter CSL-Typ:ERROR(Spalten sind nicht in Ordnung))|ERROR(Spalten sind nicht in Ordnung)|

Führen Sie mit einem Schema zusammen, `PreserveOrder` das `false` eine andere Spaltenreihenfolge aufweist, jedoch mit einer Zeiteinstellung:

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