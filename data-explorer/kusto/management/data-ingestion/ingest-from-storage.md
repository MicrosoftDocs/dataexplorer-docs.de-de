---
title: Der Befehl .ingest in (Daten aus dem Speicher abrufen) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Befehl .ingest in (Pull-Daten aus dem Speicher) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 1f304f9dc064094c6d55cb32f3fb453f32114979
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521436"
---
# <a name="the-ingest-into-command-pull-data-from-storage"></a>Der Befehl .ingest in (Pull-Daten aus dem Speicher)

Der `.ingest into` Befehl erfasst Daten in eine Tabelle, indem er die Daten aus einem oder mehreren Cloudspeicherartefakten "abzieht".
Der Befehl kann z. B. 1000 CSV-formatierte Blobs aus Azure Blob Storage abrufen, analysieren und in einer einzelnen Zieltabelle aufnehmen.
Daten werden an die Tabelle angehängt, ohne dass sich dies auf vorhandene Datensätze auswirkt und ohne das Schema der Tabelle zu ändern.

**Syntax**

`.ingest`[`async` `into` `table` ] *Tabellenname* *SourceDataLocator* `with` `(` [ *IngestionPropertyName* `=` `,` *IngestionPropertyValue* [ ...] `)`]

**Argumente**

* `async`: Wenn angegeben, wird der Befehl sofort zurückgegeben und die Aufnahme im Hintergrund fortgesetzt. Die Ergebnisse des Befehls `OperationId` enthalten einen Wert, `.show operation` der dann mit dem Befehl verwendet werden kann, um den Status und die Ergebnisse für den Abschluss der Aufnahme abzurufen.
  
* *TableName*: Der Name der Tabelle, in die Daten erfasst werden sollen.
  Der Tabellenname ist immer relativ zur Datenbank im Kontext, und sein Schema ist das Schema, das für die Daten angenommen wird, wenn kein Schemazuordnungsobjekt bereitgestellt wird.

* *SourceDataLocator*: Ein `string`Literal vom Typ oder eine durch Kommas `(` getrennte `)` Liste solcher Literale, die von und Zeichen umgeben sind, und die die Speicherartefakte angeben, die die zu ziehenden Daten enthalten. Siehe [Speicherverbindungszeichenfolgen](../../api/connection-strings/storage.md).

> [!NOTE]
> Es wird dringend empfohlen, [verschleierte Zeichenfolgenliterale](../../query/scalar-data-types/string.md#obfuscated-string-literals) für den *SourceDataPointer* zu verwenden, die tatsächliche Anmeldeinformationen enthalten.
> Der Dienst wird sicher sein, Anmeldeinformationen in seinen internen Ablaufverfolgungen, Fehlermeldungen usw. zu schrubben.

* *IngestionPropertyName*, *ingestionPropertyValue*: Eine beliebige Anzahl von [Aufnahmeeigenschaften,](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) die sich auf den Aufnahmeprozess auswirken.

**Ergebnisse**

Das Ergebnis des Befehls ist eine Tabelle mit so vielen Datensätzen, wie Datenshards ("Extents") vorhanden sind, die vom Befehl generiert werden.
Wenn keine Datenshards generiert wurden, wird ein einzelner Datensatz mit einer leeren (Nullwert)-Ausdehnungs-ID zurückgegeben.

|Name       |type      |BESCHREIBUNG                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId   |`guid`    |Der eindeutige Bezeichner für den Datenshard, der durch den Befehl generiert wurde.|
|ItemLoaded |`string`  |Mindestens ein Speicherartefakt, die mit diesem Datensatz verknüpft sind.             |
|Duration   |`timespan`|Wie lange es gedauert hat, die Einnahme durchzuführen.                                     |
|Haserrors  |`bool`    |Gibt an, ob dieser Datensatz einen Aufnahmefehler darstellt oder nicht.                |
|OperationId|`guid`    |Eine eindeutige ID, die den Vorgang darstellt. Kann mit dem `.show operation` Befehl verwendet werden.|

**Anmerkungen**

Dieser Befehl ändert nicht das Schema der Tabelle, in die aufgenommen wird.
Bei Bedarf werden die Daten während der Aufnahme in dieses Schema "genötigt" und nicht umgekehrt (zusätzliche Spalten werden ignoriert, und fehlende Spalten werden als NULL-Werte behandelt).

**Beispiele**

Im nächsten Beispiel wird das Modul angewiesen, zwei Blobs aus Azure Blob Storage als `T`CSV-Dateien zu lesen und deren Inhalt in die Tabelle aufzunehmen. Der `...` stellt eine Azure Storage Shared Access Signature (SAS) dar, die Lesezugriff auf jedes Blob gewährt. Beachten Sie auch die Verwendung von verschleierten Zeichenfolgen (die `h` vor den Zeichenfolgenwerten), um sicherzustellen, dass die SAS nie aufgezeichnet wird.

```kusto
.ingest into table T (
    h'https://contoso.blob.core.windows.net/container/file1.csv?...',
    h'https://contoso.blob.core.windows.net/container/file2.csv?...'
)
```

Das nächste Beispiel ist das Einsansinnen von Daten aus Azure Data Lake Storage Gen 2 (ADLSv2). Die hier verwendeten`...`Anmeldeinformationen ( ) sind die Speicherkontoanmeldeinformationen (gemeinsamer Schlüssel), und wir verwenden Die Zeichenfolgenverschleierung nur für den geheimen Teil der Verbindungszeichenfolge.

```kusto
.ingest into table T (
  'abfss://myfilesystem@contoso.dfs.core.windows.net/path/to/file1.csv;'
    h'...'
)
```

Im nächsten Beispiel wird eine einzelne Datei aus Azure Data Lake Storage (ADLS) erfasst.
Es verwendet die Anmeldeinformationen des Benutzers, um auf ADLS zuzugreifen (daher ist es nicht erforderlich, den Speicher-URI als mit einem geheimen Schlüssel zu behandeln). Außerdem wird gezeigt, wie Aufnahmeeigenschaften angegeben werden.

```kusto
.ingest into table T ('adl://contoso.azuredatalakestore.net/Path/To/File/file1.ext;impersonate')
  with (format='csv')
```

