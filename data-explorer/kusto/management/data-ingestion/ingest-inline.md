---
title: 'Der inerfassungs-Inline Befehl (Push): Azure Daten-Explorer'
description: In diesem Artikel wird der inerfassungs-Inline Befehl (Push) beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 2ac3a9a414d31492917cfb1768ce7bb1d7d8abb1
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257925"
---
# <a name="ingest-inline-command-push"></a>. inerfassungs-Inline Befehl (Push)

Mit diesem Befehl werden Daten in einer Tabelle erfasst, indem die Daten, die Inline eingebettet sind, per Push in den Befehls Text selbst übertragen werden.

> [!NOTE]
> Dieser Befehl wird für manuelle Ad-hoc-Tests verwendet.
> Für den Einsatz in der Produktion wird empfohlen, dass Sie andere Erfassungsmethoden verwenden, die für die Massen Übermittlung großer Datenmengen, z. b. Erfassung [aus dem Speicher](./ingest-from-storage.md), besser geeignet sind.

**Syntax**

`.ingest``inline` `into` `table` *TableName* [ `with` `(` *ingestionpropertyname* `=` *ingestionpropertyvalue* [ `,` ...] `)` ] `<|` *Daten*

**Argumente**

* *TableName* ist der Name der Tabelle, in der Daten erfasst werden sollen.
  Der Name ist immer relativ zur Datenbank im Kontext.
  Das Tabellen Schema ist das Schema, das für die Daten angenommen wird, wenn kein Schema Zuordnungsobjekt bereitgestellt wird.

* *Daten* sind die Daten, die erfasst werden sollen. Wenn diese Inhalte nicht anderweitig durch die Erfassungs Eigenschaften geändert werden, werden diese Inhalte als CSV-Daten analysiert.
 
> [!NOTE]
> Im Gegensatz zu den meisten Steuerungs Befehlen und-Abfragen muss der Text des *Daten* Teils des Befehls nicht den syntaktischen Konventionen der Sprache entsprechen. Leerzeichen sind z. b. wichtig, oder die `//` Kombination wird nicht als Kommentar behandelt.

* *Ingestionpropertyname*, *ingestionpropertyvalue*: eine beliebige Anzahl von Erfassungs [Eigenschaften](../../../ingestion-properties.md) , die sich auf den Erfassungs Vorgang auswirken.

**Ergebnisse**

Das Ergebnis des Befehls ist eine Tabelle mit so vielen Datensätzen wie generierte datenshards ("Extents").
Wenn keine datenshards generiert werden, wird ein einzelner Datensatz mit einer leeren (Nullwert-) Block-ID zurückgegeben.

|Name       |Type      |BESCHREIBUNG                                                 |
|-----------|----------|------------------------------------------------------------|
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
You can generate inline ingests commands using the Kusto.Data client library. 
(Note that compression does let you embed new lines in quoted fields) 

    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);

-->
