---
title: IngestionBatching-Richtlinie - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die IngestionBatching-Richtlinie in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 232767e390669a08312f10d3999d19264fb29f26
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744300"
---
# <a name="ingestionbatching-policy"></a>IngestionBatching-Richtlinie

Die [ingestionBatching-Richtlinie](batchingpolicy.md) ist ein Richtlinienobjekt, das bestimmt, wann die Datenaggregation während der Datenerfassung gemäß den angegebenen Einstellungen beendet werden soll.

Die Richtlinie kann `null`auf festgelegt werden, in diesem Fall werden die Standardwerte verwendet, wodurch die maximale Batchdauer auf 5 Minuten, 1000 Elemente und eine Gesamtbatchgröße von 1G oder den von Kusto festgelegten Standardclusterwert festgelegt wird.

Wenn die Richtlinie nicht für eine bestimmte Entität festgelegt ist, wird nach einer Richtlinie auf höherer Hierarchieebene gesucht, wenn alle auf NULL festgelegt sind, wird der Standardwert verwendet. 

Die Richtlinie hat eine untere Grenze von 10 Sekunden, und es wird nicht empfohlen, Werte zu verwenden, die größer als 15 Minuten sind.

## <a name="displaying-the-ingestionbatching-policy"></a>Anzeigen der IngestionBatching-Richtlinie

Die Richtlinie kann für eine Datenbank oder eine Tabelle festgelegt werden und wird mit einem der folgenden Befehle angezeigt:

* `.show``database` *Datenbankname* `policy``ingestionbatching`
* `.show``table` *DatabaseName-Tabellenname*`.`*TableName* `policy``ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>Ändern der IngestionBatching-Richtlinie

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

Ändern der IngestionBatching-Richtlinie für mehrere Tabellen (im gleichen Datenbankkontext):

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

IngestionBatching-Richtlinie:

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type`: Tabelle, Datenbank
* `database_or_table`: Wenn Entität Tabelle oder Datenbank ist, sollte ihr Name im Befehl wie folgt angegeben werden: 
  - `database_name` oder 
  - `database_name.table_name` oder 
  - `table_name`(bei Ausführung im Kontext der spezifischen Datenbank)

## <a name="deleting-the-ingestionbatching-policy"></a>Löschen der IngestionBatching-Richtlinie

```kusto
.delete <entity_type> <database_or_table_name> policy ingestionbatching
```

**Beispiele**

```kusto
// Show IngestionBatching policy for table `MyTable` in database `MyDatabase`
.show table MyDatabase.MyTable policy ingestionbatching 

// Set IngestionBatching policy on table `MyTable` (in database context) to batch ingress data by 30 seconds, 500 files, or 1GB (whatever comes first)
.alter table MyTable policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:30", "MaximumNumberOfItems": 500, "MaximumRawDataSizeMB": 1024}'

// Set IngestionBatching policy on multiple tables (in database context) to batch ingress data by 1 minute, 20 files, or 300MB (whatever comes first)
.alter tables (MyTable1, MyTable2, MyTable3) policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:01:00", "MaximumNumberOfItems": 20, "MaximumRawDataSizeMB": 300}'

// Delete IngestionBatching policy on table `MyTable`
.delete table MyTable policy ingestionbatching

// Delete IngestionBatching policy on database `MyDatabase`
.delete database MyDatabase policy ingestionbatching
```
