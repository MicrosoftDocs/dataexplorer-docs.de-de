---
title: V2-HTTP-Antwort abfragen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Abfrage V2-HTTP-Antwort in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: cca9b8381c7c59993c1e9071c46f34c1754d2429
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524309"
---
# <a name="query-v2-http-response"></a>Abfrage V2-HTTP-Antwort

Wenn der Statuscode 200 ist, ist der Antworttext ein JSON-Array.
Jedes JSON-Objekt im Array wird als _Frame_bezeichnet.

Es gibt 7 Arten von Rahmen:

1. DataSetHeader
2. TableHeader
3. TableFragment
4. TableProgress
5. TableVervollständigung
6. DataTable
7. DataSetVervollständigung

## <a name="datasetheader"></a>DataSetHeader 

Dies ist immer der erste Frame im Datensatz und wird genau einmal angezeigt.

```json
{
    "Version": string,
    "IsProgressive": Boolean
}
```

Hierbei gilt:

1. `Version`enthält die Protokollversion. Die aktuelle Version ist `v2.0`.
2. `IsProgressive`ist ein boolesches Flag, das angibt, ob dieser Datensatz progressive Frames enthält. Ein progressiver Rahmen ist einer der folgenden:
    1. `TableHeader`: Enthält allgemeine Informationen zur Tabelle
    2. `TableFragment`: Enthält einen rectangluar Daten-Shard der Tabelle
    3. `TableProgress`: Enthält den Fortschritt in Prozent (0-100)
    4. `TableCompletion`: Markiert, dass dies der letzte Frame der Tabelle ist.
        
    Die vier obigen Frames werden zusammen verwendet, um eine Tabelle zu beschreiben.
    Wenn dieses Flag nicht gesetzt ist, wird jede Tabelle im Set mit einem einzelnen Frame serialisiert:
      1. `DataTable`: Enthält alle Informationen, die der Client zu einer einzelnen Tabelle im Datensatz benötigt.


## <a name="tableheader"></a>TableHeader

Abfragen, die mit `results_progressive_enabled` der Option true ausgegeben werden, können diesen Frame enthalten. Nach dieser Tabelle sollten Clients eine `TableFragment` interleaving-Sequenz von und `TableProgress` Frames erwarten, gefolgt von einem `TableCompletion` Frame. Danach werden keine weiteren Frames mehr zu dieser Tabelle gesendet.

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
}
```

Hierbei gilt:

1. `TableId`ist die eindeutige ID der Tabelle.
2. `TableKind`ist die Art der Tabelle, die eine der folgenden sein kann:

      * Primäres Ergebnis
      * QueryCompletionInformation
      * QueryTraceLog
      * QueryPerfLog
      * TableOfContents
      * QueryProperties
      * QueryPlan
      * Unknown
3. `TableName`ist der Name der Tabelle.
4. `Columns`ist ein Array, das das Schema der Tabelle beschreibt:

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```
Unterstützte Spaltentypen werden [hier](../../query/scalar-data-types/index.md)beschrieben.

## <a name="tablefragment"></a>TableFragment

Dieser Rahmen enthält ein rechteckiges Datenfragment der Tabelle. Zusätzlich zu den tatsächlichen Daten enthält `TableFragmentType` dieser Frame eine Eigenschaft, die dem Client mitteilt, was mit dem Fragment zu tun ist (es kann entweder an vorhandene Fragmente angehängt werden oder sie alle zusammen ersetzen).

```json
{
    "TableId": Number,
    "FieldCount": Number,
    "TableFragmentType": string,
    "Rows": Array
}
```

Hierbei gilt:

1. `TableId`ist die eindeutige ID der Tabelle.
2. `FieldCount`ist die Anzahl der Spalten in der Tabelle
3. `TableFragmentType`beschreibt, was der Client mit diesem Fragment tun soll. Dabei kann es sich um eine der folgenden Methoden handeln:
      * DataAppend
      * DataReplace
4. `Rows`ist ein zweidimensionales Array, das die Fragmentdaten enthält.

## <a name="tableprogress"></a>TableProgress

Dieser Rahmen kann mit `TableFragment` dem oben beschriebenen Rahmen überflochten werden.
Der einzige Zweck besteht darin, den Client über den Abfragefortschritt zu benachrichtigen.

```json
{
    "TableId": Number,
    "TableProgress": Number,
}
```

Hierbei gilt:

1. `TableId`ist die eindeutige ID der Tabelle.
2. `TableProgress`ist der Fortschritt in Prozent (0--100).

## <a name="tablecompletion"></a>TableVervollständigung

Die `TableCompletion` Rahmen markieren das Ende der Tischübertragung. Es werden keine weiteren Frames gesendet, die sich auf diese Tabelle beziehen.

```json
{
    "TableId": Number,
    "RowCount": Number,
}
```    

Hierbei gilt:

1. `TableId`ist die eindeutige ID der Tabelle.
2. `RowCount`ist die letzte Anzahl von Zeilen in der Tabelle.

## <a name="datatable"></a>DataTable

Abfragen, die mit `EnableProgressiveQuery` dem auf false gesetzten Flag ausgegeben`TableHeader`werden, enthalten keine der vorherigen 4 Frames ( , `TableFragment`, `TableProgress` und `TableCompletion`). Stattdessen wird jede Tabelle im Datensatz mit einem einzigen `DataTable` Frame, dem Frame, übertragen, der alle Informationen enthält, die der Client zum Lesen der Tabelle benötigt.

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

1. `TableId`ist die eindeutige ID der Tabelle.
2. `TableKind`ist die Art der Tabelle, die eine der folgenden sein kann:

      * Primäres Ergebnis
      * QueryCompletionInformation
      * QueryTraceLog
      * QueryPerfLog
      * QueryProperties
      * QueryPlan
      * Unknown
3. `TableName`ist der Name der Tabelle.
4. `Columns`ist ein Array, das das Schema der Tabelle beschreibt:

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```
4. `Rows`ist ein zweidimensionales Array, das die Daten der Tabelle enthält.

### <a name="the-meaning-of-tables-in-the-response"></a>Die Bedeutung von Tabellen in der Antwort

* `PrimaryResult`- Das wichtigste tabellarische Ergebnis der Abfrage. Für jede [tabellenförmige Ausdrucksanweisung](../../query/tabularexpressionstatements.md)werden eine oder mehrere Tabellen in der Reihenfolge emittiert, die die von der Anweisung erzeugten Ergebnisse darstellen (aufgrund von [Batches](../../query/batches.md) und [Gabeloperatoren](../../query/forkoperator.md)können mehrere solcher Tabellen vorhanden sein).
* `QueryCompletionInformation`- stellt zusätzliche Informationen zur Ausführung der Abfrage selbst bereit, z. B. ob sie erfolgreich abgeschlossen wurde oder nicht, und welche Ressourcen von der Abfrage verbraucht wurden (ähnlich der QueryStatus-Tabelle in der v1-Antwort). 
* `QueryProperties`- stellt zusätzliche Werte wie Client-Visualisierungsanweisungen (z. B. emittiert, um die Informationen im [Renderoperator](../../query/renderoperator.md)widerzuspiegeln) und [Datenbankcursorinformationen](../../management/databasecursor.md) bereit).
* `QueryTraceLog`- Leistungsablaufverfolgungsprotokollinformationen (zurückgegeben beim Festlegen von perftrace in [Clientanforderungseigenschaften](../netfx/request-properties.md)).

## <a name="datasetcompletion"></a>DataSetVervollständigung

Dies ist der letzte Frame im Datensatz.
```json
{
    "HasErrors": Boolean,
    "Cancelled": Boolean,
    "OneApiErrors": Array,
}
```

Hierbei gilt:

1. `HasErrors`ist wahr, wenn Fehler beim Generieren des Datensatzes aufgetreten sind.
2. `Cancelled`ist wahr, wenn die Anforderung, die zur Generierung des Datensatzes führt, auf halbem Weg abgebrochen wurde. 
3. `OneApiErrors`wird nur `HasErrors` übertragen, wenn dies wahr ist. Eine Beschreibung des `OneApiErrors` Formats finden Sie in Abschnitt 7.10.2 [hier](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md).