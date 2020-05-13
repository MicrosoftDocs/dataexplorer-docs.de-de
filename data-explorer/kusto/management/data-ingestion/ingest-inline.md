---
title: 'Der inerfassungs-Inline Befehl (Push): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der inerfassungs-Inline Befehl (Push) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 2b1766b295fd348639d8d91c8308a3ed0a35a3dc
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373411"
---
# <a name="the-ingest-inline-command-push"></a>Der inerfassungs-Inline Befehl (Push)

Mit diesem Befehl werden Daten in einer Tabelle erfasst, indem die Daten, die Inline in den Befehls Text eingebettet sind, per Push übertragen werden.

> [!NOTE]
> Der Hauptzweck dieses Befehls ist für manuelle Ad-hoc-Testzwecke vorgesehen.
> Für die Produktion wird empfohlen, andere Erfassungsmethoden zu verwenden, die für die Massen Übermittlung großer Datenmengen geeignet sind, wie z. b. die Erfassung [aus dem Speicher](./ingest-from-storage.md).

**Syntax**

`.ingest``inline` `into` `table` *TableName* [ `with` `(` *ingestionpropertyname* `=` *ingestionpropertyvalue* [ `,` ...] `)` ] `<|` *Daten*



**Argumente**

* *TableName* ist der Name der Tabelle, in der Daten erfasst werden sollen.
  Der Tabellenname ist immer relativ zur Datenbank im Kontext, und sein Schema ist das Schema, das für die Daten angenommen wird, wenn kein Schema Zuordnungsobjekt bereitgestellt wird.

* *Daten* sind die Daten, die erfasst werden sollen. Wenn diese Inhalte nicht anderweitig durch die Erfassungs Eigenschaften geändert werden, werden diese Inhalte als CSV-Daten analysiert.
  Beachten Sie, dass im Gegensatz zu den meisten Steuerungs Befehlen und-Abfragen der Text des *Daten* Teils des Befehls nicht den syntaktischen Konventionen der Sprache entsprechen muss (z. b. sind Leerzeichen wichtig, die `//` Kombination wird nicht als Kommentar behandelt usw.).

* *Ingestionpropertyname*, *ingestionpropertyvalue*: eine beliebige Anzahl von Erfassungs [Eigenschaften](../../../ingestion-properties.md) , die sich auf den Erfassungs Vorgang auswirken.

**Ergebnisse**

Das Ergebnis des Befehls ist eine Tabelle mit so vielen Datensätzen, wie Daten-Shards ("Extents") vom Befehl generiert werden.
Wenn keine datenshards generiert wurden, wird ein einzelner Datensatz mit einer leeren (Nullwert-) Block-ID zurückgegeben.

|Name       |type      |Beschreibung                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|Extentid   |`guid`    |Der eindeutige Bezeichner für den Daten-Shard, der durch den Befehl generiert wurde.|

**Beispiele**

Der folgende Befehl erfasst Daten in eine Tabelle ( `Purchases` ) mit zwei Spalten `SKU` (vom Typ `string` ) und `Quantity` (vom Typ `long` ).

```kusto
.ingest inline into table Purchases <|
Shoes,1000
Wide Shoes,50
"Coats, black",20
"Coats with ""quotes""",5
```



<!--
It is possible to generate inline ingests commands using the Kusto.Data client library. (Note that compression does allow one to embed newlines in quoted fields) 

    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);

-->