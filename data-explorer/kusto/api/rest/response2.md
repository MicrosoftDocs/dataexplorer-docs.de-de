---
title: 'Abfrage v2 HTTP-Antwort: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die HTTP-Antwort Query v2 in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 86a56d77005b2c6b5c9d38bbec85eebfbcb481dc
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617900"
---
# <a name="query-v2-http-response"></a>Query v2-HTTP-Antwort

Wenn der Statuscode 200 lautet, ist der Antworttext ein JSON-Array.
Jedes JSON-Objekt im Array wird als _Frame_bezeichnet.

Es gibt mehrere Arten von Frames:

* [Datasederader](#datasetheader)
* [TableHeader](#tableheader)
* [Tablefragment](#tablefragment)
* [Tableprogress](#tableprogress)
* [Tablecompletion](#tablecompletion)
* [DataTable](#datatable)
* [Datasetcompletion](#datasetcompletion)

## <a name="datasetheader"></a>Datasederader 

Der `DataSetHeader` Frame ist immer der erste im DataSet und wird genau einmal angezeigt.

```json
{
    "Version": string,
    "IsProgressive": Boolean
}
```

Hierbei gilt:

* `Version`die Protokollversion. Die aktuelle Version ist `v2.0`.
* `IsProgressive`ein boolesches Flag, das angibt, ob dieses DataSet progressive Frames enthält. 
   Ein progressiver Frame ist einer der folgenden:
   
     | Frame             | BESCHREIBUNG                                    |
     |-------------------| -----------------------------------------------|
     | `TableHeader`     | Enthält allgemeine Informationen zur Tabelle.   |
     | `TableFragment`   | Enthält einen rechteckigen datenshard der Tabelle. |
     | `TableProgress`   | Enthält den Fortschritt in Prozent (0-100).       |
     | `TableCompletion` | Gibt an, dass dieser Frame der letzte ist.      |
        
    Die obigen Frames beschreiben eine Tabelle.
    Wenn das `IsProgressive` Flag nicht auf true festgelegt ist, wird jede Tabelle im Satz mit einem einzelnen Frame serialisiert:
* `DataTable`: Enthält alle Informationen, die der Client zu einer einzelnen Tabelle im DataSet benötigt.

## <a name="tableheader"></a>TableHeader

Abfragen, die mit der `results_progressive_enabled` Option auf true festgelegt werden, können diesen Frame einschließen. Im Anschluss an diese Tabelle können Clients eine Austausch Ende Sequenz von `TableFragment` - `TableProgress` und-Frames erwarten. Der abschließende Rahmen der Tabelle ist `TableCompletion`.

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
}
```

Hierbei gilt:

* `TableId`die eindeutige ID der Tabelle.
* `TableKind`ist eine von:

    * Primaryresult
    * Querycompletioninformation
    * Querytracelog
    * Queryperflog
    * TableOfContents
    * QueryProperties
    * QueryPlan
    * Unknown
      
* `TableName`der Name der Tabelle.
* `Columns`ein Array, das das Schema der Tabelle beschreibt.

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```

Unterstützte Spaltentypen werden [hier](../../query/scalar-data-types/index.md)beschrieben.

## <a name="tablefragment"></a>Tablefragment

Der `TableFragment` Frame enthält ein rechteckiges Daten Fragment der Tabelle. Zusätzlich zu den eigentlichen Daten enthält dieser Frame auch eine `TableFragmentType` -Eigenschaft, die dem Client mitteilt, was mit dem Fragment zu tun ist. Das an vorhandene Fragmente angefügte Fragment oder ersetzt diese.

```json
{
    "TableId": Number,
    "FieldCount": Number,
    "TableFragmentType": string,
    "Rows": Array
}
```

Hierbei gilt:

* `TableId`die eindeutige ID der Tabelle.
* `FieldCount`die Anzahl der Spalten in der Tabelle.
* `TableFragmentType`Beschreibt die Aktionen, die der Client mit diesem Fragment ausführen sollte. 
    `TableFragmentType`ist eine von:
    
    * Dataappend
    * Datareplace
      
* `Rows`ist ein zweidimensionales Array, das die Fragmentdaten enthält.

## <a name="tableprogress"></a>Tableprogress

Der `TableProgress` Frame kann mit dem `TableFragment` oben beschriebenen Frame Interleave werden.
Der einzige Zweck besteht darin, den Client über den Fortschritt der Abfrage zu benachrichtigen.

```json
{
    "TableId": Number,
    "TableProgress": Number,
}
```

Hierbei gilt:

* `TableId`die eindeutige ID der Tabelle.
* `TableProgress`der Fortschritt in Prozent (0--100).

## <a name="tablecompletion"></a>Tablecompletion

Der `TableCompletion` Rahmen markiert das Ende der Tabellen Übertragung. Es werden keine weiteren Frames in Bezug auf diese Tabelle gesendet.

```json
{
    "TableId": Number,
    "RowCount": Number,
}
```    

Hierbei gilt:

* `TableId`die eindeutige ID der Tabelle.
* `RowCount`die Gesamtanzahl der Zeilen in der Tabelle.

## <a name="datatable"></a>DataTable

Abfragen, die mit dem `EnableProgressiveQuery` -Flag ausgegeben werden, das auf false festgelegt ist,`TableHeader`enthalten `TableFragment`keine `TableProgress`der Frames `TableCompletion`(,, und). Stattdessen wird jede Tabelle im DataSet mit dem `DataTable` Frame übertragen, der alle Informationen enthält, die der Client benötigt, um die Tabelle zu lesen.

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
    "Rows": Array,
}
```    

Hierbei gilt:

* `TableId`die eindeutige ID der Tabelle.
* `TableKind`ist eine von:

    * Primaryresult
    * Querycompletioninformation
    * Querytracelog
    * Queryperflog
    * QueryProperties
    * QueryPlan
    * Unknown
      
* `TableName`der Name der Tabelle.
* `Columns`ist ein Array, das das Schema der Tabelle beschreibt und Folgendes umfasst:

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```

* `Rows`ist ein zweidimensionales Array, das die Daten der Tabelle enthält.

### <a name="the-meaning-of-tables-in-the-response"></a>Die Bedeutung von Tabellen in der Antwort.

* `PrimaryResult`: Das Haupt Tabellen Ergebnis der Abfrage. Für jede [tabellarische Ausdrucks Anweisung](../../query/tabularexpressionstatements.md)werden eine oder mehrere Tabellen in der Reihenfolge generiert, die die von der Anweisung erzeugten Ergebnisse darstellt. Es können mehrere solcher Tabellen vorhanden sein, weil [Batches](../../query/batches.md) und [Fork-Operatoren](../../query/forkoperator.md)vorhanden sind.
* `QueryCompletionInformation`: Stellt zusätzliche Informationen über die Ausführung der Abfrage selbst bereit, z. b. ob Sie erfolgreich abgeschlossen wurde oder nicht, und welche Ressourcen von der Abfrage genutzt wurden (ähnlich der QueryStatus-Tabelle in der v1-Antwort). 
* `QueryProperties`-Bietet zusätzliche Werte, wie z. b. Client Visualisierungs Anweisungen (z. b., um die Informationen im [Rendering-Operator](../../query/renderoperator.md)widerzuspiegeln) und [Daten Bank Cursor](../../management/databasecursor.md) Informationen).
* `QueryTraceLog`-Die Ablauf Verfolgungs Protokoll-Informationen für die `perftrace` Leistung (zurückgegeben, wenn in [Client Anforderungs Eigenschaften](../netfx/request-properties.md) auf true festgelegt ist).

## <a name="datasetcompletion"></a>Datasetcompletion

Der `DataSetCompletion` Frame ist der letzte im DataSet.

```json
{
    "HasErrors": Boolean,
    "Cancelled": Boolean,
    "OneApiErrors": Array,
}
```

Hierbei gilt:

* `HasErrors`ist true, wenn beim Erzeugen des Datasets Fehler aufgetreten sind.
* `Cancelled`ist true, wenn die Anforderung, die zur Generierung des Datasets geführt hat, vor dem Abschluss abgebrochen wurde. 
* `OneApiErrors`wird nur zurückgegeben `HasErrors` , wenn den Wert true hat. Eine Beschreibung des Formats finden `OneApiErrors` Sie im Abschnitt 7.10.2 [hier](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md).
