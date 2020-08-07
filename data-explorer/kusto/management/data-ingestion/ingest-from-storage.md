---
title: 'Kusto. Einzug in den Befehl (Daten aus dem Speicher abrufen): Azure-Daten-Explorer'
description: In diesem Artikel wird der Befehl zum Erfassen von Daten in Azure Daten-Explorer beschrieben (Pull Data from Storage).
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 90158c066c724f7d05e9c5e91333874ad572e81b
ms.sourcegitcommit: 53679e57e0fcb7ec46beaba7cc812bd991da6cc0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87984469"
---
# <a name="the-ingest-into-command-pull-data-from-storage"></a>Der. infassungs into-Befehl (Pull Data aus dem Speicher)

Der `.ingest into` Befehl erfasst Daten in eine Tabelle, indem er die Daten aus einer oder mehreren cloudspeicherdateien abzieht.
Beispielsweise können mit dem Befehl 1000 CSV-formatierte BLOB aus Azure BLOB Storage abgerufen, analysiert und in einer einzigen Ziel Tabelle erfasst werden.
Daten werden an die Tabelle angehängt, ohne dass sich dies auf vorhandene Datensätze auswirkt, ohne das Schema der Tabelle zu ändern.

**Syntax**

`.ingest`[ `async` ] `into` `table` *TableName* *sourcedatalocator* [ `with` `(` *ingestionpropertyname* `=` *ingestionpropertyvalue* [ `,` ...] `)` ]

**Argumente**

* `async`: Wenn dieser Wert angegeben wird, wird der Befehl sofort zurückgegeben, und die Erfassung im Hintergrund wird fortgesetzt. Die Ergebnisse des Befehls enthalten einen `OperationId` Wert, der dann mit dem Befehl verwendet werden kann `.show operation` , um den Erfassungs Status und die Ergebnisse abzurufen.
  
* *TableName*: der Name der Tabelle, in der Daten erfasst werden sollen.
  Der Tabellenname ist immer relativ zur Datenbank im Kontext, und sein Schema ist das Schema, das für die Daten angenommen wird, wenn kein Schema Zuordnungsobjekt bereitgestellt wird.

* *Sourcedatalocator: ein Literaltyp* `string` oder eine durch Trennzeichen getrennte Liste mit solchen Literalen, die von `(` -und- `)` Zeichen umgeben sind und [Speicher Verbindungs](../../api/connection-strings/storage.md)Zeichenfolgen darstellen. Kusto verwendet ein URI-Format zum Beschreiben der Speicherdateien, die die Daten enthalten, die abgerufen werden sollen. 
  * Eine einzelne Verbindungs Zeichenfolge muss auf eine einzelne Datei verweisen, die von einem Speicherkonto gehostet wird. 
  * Die Erfassung mehrerer Dateien kann durch Angabe mehrerer Verbindungs Zeichenfolgen erfolgen, die durch Kommas getrennt sind, oder durch die Erfassung [aus einer Abfrage](ingest-from-query.md) einer [externen Tabelle](../../query/schema-entities/externaltables.md).

> [!NOTE]
> Es wird dringend empfohlen, verborgene [Zeichen folgen Literale](../../query/scalar-data-types/string.md#obfuscated-string-literals) für den *sourcedatapointer* zu verwenden, der tatsächliche Anmelde Informationen enthält.
> Der Dienst wird sicher sein, dass Anmelde Informationen in seinen internen Ablauf Verfolgungen, Fehlermeldungen usw. bereinigt werden.

* *Ingestionpropertyname*, *ingestionpropertyvalue*: eine beliebige Anzahl von Erfassungs [Eigenschaften](../../../ingestion-properties.md) , die sich auf den Erfassungs Vorgang auswirken.

**Ergebnisse**

Das Ergebnis des Befehls ist eine Tabelle mit so vielen Datensätzen, wie Daten-Shards ("Extents") vom Befehl generiert werden.
Wenn keine datenshards generiert wurden, wird ein einzelner Datensatz mit einer leeren (Nullwert-) Block-ID zurückgegeben.

|Name       |Typ      |BESCHREIBUNG                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|Extentid   |`guid`    |Der eindeutige Bezeichner für den Daten-Shard, der durch den Befehl generiert wurde.|
|Itemloaded |`string`  |Eine oder mehrere Speicherdateien, die mit diesem Datensatz verknüpft sind.             |
|Duration   |`timespan`|Wie lange es gedauert hat, bis die Erfassung durchgeführt wurde.                                     |
|HasErrors  |`bool`    |Gibt an, ob dieser Datensatz einen Erfassungs Fehler darstellt oder nicht.                |
|OperationId|`guid`    |Eine eindeutige ID, die den Vorgang darstellt. Kann mit dem Befehl verwendet werden `.show operation` .|

**Anmerkungen**

Mit diesem Befehl wird das Schema der Tabelle, in der die Erfassung erfolgt, nicht geändert.
Bei Bedarf werden die Daten während der Erfassung in dieses Schema umgewandelt, nicht umgekehrt (zusätzliche Spalten werden ignoriert, und fehlende Spalten werden als NULL-Werte behandelt).

**Beispiele**

Im nächsten Beispiel wird die Engine angewiesen, zwei BLOB-Daten aus Azure BLOB Storage als CSV-Dateien zu lesen und ihren Inhalt in die-Tabelle zu erfassen `T` . `...`Stellt eine Azure Storage Shared Access Signature (SAS) dar, die jedem BLOB Lesezugriff gewährt. Beachten Sie auch die Verwendung von verborgenen Zeichen folgen (vor `h` den Zeichen folgen Werten), um sicherzustellen, dass die SAS nie aufgezeichnet wird.

```kusto
.ingest into table T (
    h'https://contoso.blob.core.windows.net/container/file1.csv?...',
    h'https://contoso.blob.core.windows.net/container/file2.csv?...'
)
```

Das nächste Beispiel ist das Erfassen von Daten aus Azure Data Lake Storage Gen 2 (Gen 2) (ADLSv2). Bei den hier verwendeten Anmelde Informationen ( `...` ) handelt es sich um die Anmelde Informationen für das Speicherkonto (Shared Key), und wir verwenden die Zeichen folgen Verschleierung nur für den geheimen Teil der Verbindungs Zeichenfolge.

```kusto
.ingest into table T (
  'abfss://myfilesystem@contoso.dfs.core.windows.net/path/to/file1.csv;...'
)
```

Im nächsten Beispiel wird eine einzelne Datei aus Azure Data Lake Storage (ADLS) erfasst.
Er verwendet die Anmelde Informationen des Benutzers für den Zugriff auf ADLS (es ist also nicht erforderlich, den Speicher-URI als einen geheimen Schlüssel zu behandeln). Außerdem wird gezeigt, wie Sie Erfassungs Eigenschaften angeben.

```kusto
.ingest into table T ('adl://contoso.azuredatalakestore.net/Path/To/File/file1.ext;impersonate')
  with (format='csv')
```
