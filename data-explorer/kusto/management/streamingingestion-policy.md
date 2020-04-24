---
title: Verwaltung von Streaming-Erfassungsrichtlinien - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Verwaltung von Streaming-Erfassungsrichtlinien in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: b0b1a76e52688dcc88ca87023309f9c970b4c702
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519617"
---
# <a name="streaming-ingestion-policy-management"></a>Streaming-Erfassungsrichtlinienverwaltung

Streaming-Erfassungsrichtlinien können an eine Datenbank oder Tabelle angefügt werden, um die Streamingaufnahme an diese Speicherorte zu ermöglichen. Die Richtlinie definiert auch die Zeilenspeicher, die für die Streamingaufnahme verwendet werden.

Weitere Informationen zur Streaming-Aufnahme finden Sie unter [Streaming-Aufnahme (Vorschau)](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming). Weitere Informationen zur Streamingerfassungsrichtlinie finden Sie unter [Streaming-Aufnahmerichtlinie](streamingingestionpolicy.md).

## <a name="show-policy-streamingingestion"></a>.show-Richtlinienstreamingestion

Der `.show policy streamingingestion` Befehl zeigt die Streamingerfassungsrichtlinie der Datenbank oder Tabelle an.

**Syntax**

`.show``database` `policy` `streamingingestion` MyDatabase 
 `.show` `policy` `table` MyTable`streamingingestion`

**Rückgabe**

Dieser Befehl gibt eine Tabelle mit den folgenden Spalten zurück:

|Column    |type    |BESCHREIBUNG
|---|---|---
|PolicyName|`string`|Der Richtlinienname - StreamingIngestionPolicy
|EntityName|`string`|Datenbank- oder Tabellenname
|Richtlinie    |`string`|Ein JSON-Objekt, das die Streaming-Aufnahmerichtlinie definiert, formatiert als [Streaming-Aufnahmerichtlinienobjekt](#streaming-ingestion-policy-object)

**Beispiel**

```kusto
.show database DB1 policy streamingingestion 
.show table T1 policy streamingingestion 
```

|PolicyName|EntityName|Richtlinie|ChildEntities|EntityType|
|---|---|---|---|---|
|StreamingIngestionPolicy|DB1|»NumberOfRowStores': 4

### <a name="streaming-ingestion-policy-object"></a>Streaming-Aufnahmerichtlinienobjekt

|Eigenschaft  |type    |BESCHREIBUNG                                                       |
|----------|--------|------------------------------------------------------------------|
|NumberOfRowStores |`int`  |Die Anzahl der Zeilenspeicher, die der Entität zugewiesen sind|
|SealIntervalLimit|`TimeSpan?`|Optionales Limit für die Intervalle zwischen Dichtungsvorgängen auf der Tabelle. Der gültige Bereich liegt zwischen 1 und 24 Stunden. Standardwert: 24 Stunden.|
|SealThresholdBytes|`int?`|Optionales Limit für die Datenmenge, die für einen einzelnen Dichtungsvorgang auf der Tabelle zu nehmen ist. Der gültige Bereich für den Wert liegt zwischen 10 und 200 MBs. Standard: 200 MBs.|

## <a name="alter-policy-streamingingestion"></a>.ändern Richtlinienstreamingestion

Der `.alter policy streamingingestion` Befehl legt die Streamingestionsrichtlinie der Datenbank oder Tabelle fest.

**Syntax**

* `.alter``database` MyDatabase `policy` `streamingingestion` *StreamingIngestionPolicyObject*

* `.alter``database` MyDatabase `policy` `streamingingestion``enable`

* `.alter``database` MyDatabase `policy` `streamingingestion``disable`

* `.alter``table` MyTable `policy` `streamingingestion` *StreamingIngestionPolicyObject*

* `.alter``table` MyTable `policy` `streamingingestion``enable`

* `.alter``table` MyTable `policy` `streamingingestion``disable`

**Hinweise**

1. *StreamingIngestionPolicyObject* ist ein JSON-Objekt, für das das Streaming-Erfassungsrichtlinienobjekt definiert ist.

2. `enable`- Legen Sie die Streaming-Aufnahmerichtlinie auf 4 Rowstores fest, wenn die Richtlinie nicht oder bereits mit 0 Rowstores definiert ist, andernfalls wird der Befehl nichts bewirken.

3. `disable`- Legen Sie die Streaming-Aufnahmerichtlinie auf 0 Rowstores fest, wenn die Richtlinie bereits mit positiven Rowstores definiert ist, andernfalls wird der Befehl nichts bewirken.

**Beispiel**

```kusto
.alter database MyDatabase policy streamingingestion '{  "NumberOfRowStores": 4}'

.alter table MyTable policy streamingingestion '{  "NumberOfRowStores": 4}'
```

## <a name="delete-policy-streamingingestion"></a>.delete-Richtlinienstreamingestion

Der `.delete policy streamingingestion` Befehl löscht die Streamingestionsrichtlinie aus der Datenbank oder Tabelle.

**Syntax** 

`.delete``database` MyDatabase `policy``streamingingestion`

`.delete``table` MyTable `policy``streamingingestion`

**Rückgabe**

Der Befehl löscht das Richtlinienobjekt für die Tabellen- oder Datenbankstreamingestion und gibt dann die Ausgabe des entsprechenden [.show-Richtlinienstreamingestionsbefehls](#show-policy-streamingingestion) zurück.

**Beispiel**

```kusto
.delete database MyDatabase policy streamingingestion 
```