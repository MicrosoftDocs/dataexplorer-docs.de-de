---
title: Der Befehl .ingest inline (push) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den .ingest Inline-Befehl (Push) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 47da2df6ff974afb5e91224ade695dc0863b6817
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521368"
---
# <a name="the-ingest-inline-command-push"></a>Der .ingest Inline-Befehl (Push)

Dieser Befehl erfasst Daten in eine Tabelle, indem die Daten, die inline in den Befehlstext selbst eingebettet sind, "pushen".

> [!NOTE]
> Der Hauptzweck dieses Befehls dient manuellen Ad-hoc-Testzwecken.
> Für die Produktion wird empfohlen, andere Erfassungsmethoden zu verwenden, die für die Massenzustellung großer Datenmengen, wie z. B. [die Erfassung aus dem Speicher,](./ingest-from-storage.md)besser geeignet sind.

**Syntax**

`.ingest``inline` `(` *TableName* `with` `=` *IngestionPropertyValue* `,` *IngestionPropertyName* TableName [ IngestionPropertyName IngestionPropertyValue [ .] `into` `table` `)`] `<|` *Daten*



**Argumente**

* *TableName* ist der Name der Tabelle, in die Daten erfasst werden sollen.
  Der Tabellenname ist immer relativ zur Datenbank im Kontext, und sein Schema ist das Schema, das für die Daten angenommen wird, wenn kein Schemazuordnungsobjekt bereitgestellt wird.

* *Daten* sind der zu erfassende Dateninhalt. Sofern nicht anders durch die Aufnahmeeigenschaften geändert, wird dieser Inhalt als CSV analysiert.
  Beachten Sie, dass im Gegensatz zu den meisten Steuerbefehlen und Abfragen der Text des *Datenteils* des Befehls nicht den `//` syntaktischen Konventionen der Sprache folgen muss (z. B. sind Leerzeichen wichtig, die Kombination wird nicht als Kommentar behandelt usw.)

* *IngestionPropertyName*, *ingestionPropertyValue*: Eine beliebige Anzahl von [Aufnahmeeigenschaften,](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) die sich auf den Aufnahmeprozess auswirken.

**Ergebnisse**

Das Ergebnis des Befehls ist eine Tabelle mit so vielen Datensätzen, wie Datenshards ("Extents") vorhanden sind, die vom Befehl generiert werden.
Wenn keine Datenshards generiert wurden, wird ein einzelner Datensatz mit einer leeren (Nullwert)-Ausdehnungs-ID zurückgegeben.

|Name       |type      |BESCHREIBUNG                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId   |`guid`    |Der eindeutige Bezeichner für den Datenshard, der durch den Befehl generiert wurde.|

**Beispiele**

Der folgende Befehl erfasst Daten in`Purchases`einer Tabelle `SKU` ( ) `string`mit `Quantity` zwei `long`Spalten , (vom Typ ) und (vom Typ ).

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