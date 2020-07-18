---
title: Befehl "Kusto ingestionbatching Policy Management"-Azure Daten-Explorer
description: Dieser Artikel beschreibt den Befehl "ingestionbatching Policy" in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 04c59b33d780db1c9731ac71d1f905315afbc302
ms.sourcegitcommit: 4405ae34e119948778e0de5021077638d24da812
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/17/2020
ms.locfileid: "86448043"
---
# <a name="ingestionbatching-policy-command"></a>Befehl "ingestionbatching Policy"

Die [ingestionbatching-Richtlinie](batchingpolicy.md) ist ein Richtlinien Objekt, das bestimmt, wann die Datenaggregation während der Datenerfassung gemäß den angegebenen Einstellungen beendet werden soll.

Die Richtlinie kann auf festgelegt werden. `null` in diesem Fall werden die Standardwerte verwendet. dabei wird die maximale Batch Verarbeitungszeit Spanne festgelegt auf: 5 Minuten, 1000 Elemente und die Batch Größe insgesamt 1 g oder der von Kusto festgelegte Standard Cluster Wert.

Wenn die Richtlinie für eine bestimmte Entität nicht festgelegt ist, wird eine Richtlinie für eine höhere Hierarchieebene gesucht, wenn alle auf NULL festgelegt sind, wird der Standardwert verwendet. 

Die Richtlinie weist eine niedrigere Grenze von 10 Sekunden auf, und es wird nicht empfohlen, Werte zu verwenden, die größer als 15 Minuten sind.

## <a name="displaying-the-ingestionbatching-policy"></a>Anzeigen der ingestionbatching-Richtlinie

Die Richtlinie kann für eine Datenbank oder Tabelle festgelegt werden und wird mit einem der folgenden Befehle angezeigt:

* `.show` `database` *DatabaseName* `policy` `ingestionbatching`
* `.show``table` *DatabaseName*( `.` *Tabellenname* `policy` )`ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>Ändern der ingestionbatching-Richtlinie

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

Ändern der ingestionbatching-Richtlinie für mehrere Tabellen (im selben Daten Bank Kontext):

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

Ingestionbatching-Richtlinie:

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type`: Tabelle, Datenbank
* `database_or_table`: Wenn die Entität eine Tabelle oder Datenbank ist, sollte Ihr Name wie folgt im Befehl angegeben werden: 
  - `database_name` oder 
  - `database_name.table_name` oder 
  - `table_name`(bei Ausführung im Kontext einer bestimmten Datenbank)

## <a name="deleting-the-ingestionbatching-policy"></a>Löschen der ingestionbatching-Richtlinie

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
