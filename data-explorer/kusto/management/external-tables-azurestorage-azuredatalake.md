---
title: Externe Tabellen in Azure Storage oder Azure Data Lake-Azure-Daten-Explorer
description: In diesem Artikel wird die Verwaltung externer Tabellen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: db99d1d46c321bff0f5d7b370766900ea7d1d5a0
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227722"
---
# <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Externe Tabellen in Azure Storage oder Azure Data Lake

Mit dem folgenden Befehl wird beschrieben, wie eine externe Tabelle erstellt wird. Die Tabelle kann sich in Azure BLOB Storage, Azure Data Lake Store Gen1 oder Azure Data Lake Store Gen2 befinden. 
[Speicher Verbindungs](../api/connection-strings/storage.md) Zeichenfolgen beschreiben das Erstellen der Verbindungs Zeichenfolge für jede dieser Optionen. 

## <a name="create-or-alter-external-table"></a>. Create oder. Alter externe Tabelle

**Syntax**

( `.create`  |  `.alter` ) `external` `table` *TableName* (*Schema*)  
`kind` `=` (`blob` | `adl`)  
[ `partition` `by` *Partition* [ `,` ...]]  
`dataformat` `=` *Format*  
`(`  
*Storageconnectionstring* [ `,` ...]  
`)`  
[ `with` `(` [ `docstring` `=` *Dokumentation*] [ `,` `folder` `=` *FolderName*], *property_name* `=` *Wert* `,` ... `)` ]

Erstellt oder ändert eine neue externe Tabelle in der Datenbank, in der der Befehl ausgeführt wird.

**Parameter**

* *TableName* -Name der externen Tabelle. Muss den Regeln für [Entitäts Namen](../query/schema-entities/entity-names.md)folgen. Eine externe Tabelle kann nicht den gleichen Namen wie eine reguläre Tabelle in derselben Datenbank aufweisen.
* *Schema* -externes Datenschema im Format: `ColumnName:ColumnType[, ColumnName:ColumnType ...]` . Wenn das externe Datenschema unbekannt ist, verwenden Sie das [infer_storage_schema](../query/inferstorageschemaplugin.md) -Plug-in, das das Schema basierend auf dem Inhalt externer Dateien ableiten kann.
* *Partition* : eine oder mehrere Partitions Definitionen (optional). Siehe Partitions Syntax weiter unten.
* *Format* : das Datenformat. Alle Erfassungs [Formate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) werden für Abfragen unterstützt. Die Verwendung einer externen Tabelle für das [Export Szenario](data-export/export-data-to-an-external-table.md) ist auf die folgenden Formate beschränkt: `CSV` , `TSV` , `JSON` , `Parquet` .
* *Storageconnectionstring* : ein oder mehrere Pfade zu Azure BLOB Storage BLOB-Containern oder Azure Data Lake Store Dateisystemen (virtuelle Verzeichnisse oder Ordner), einschließlich der Anmelde Informationen. Weitere Informationen finden Sie unter [Speicher Verbindungs](../api/connection-strings/storage.md) Zeichenfolgen. Geben Sie mehr als ein einzelnes Speicherkonto an, um eine Speicher Drosselung zu vermeiden, während Sie große Datenmengen in die externe Tabelle [exportieren](data-export/export-data-to-an-external-table.md) . Beim Export werden die Schreibvorgänge zwischen allen bereitgestellten Konten verteilt. 

**Partitions Syntax**

[ `format_datetime =` *Datetimepartitionformat*] `bin(` *Timestampcolumnname*, *partitionbytimespan*`)`  
|   
[*Stringformatprefix*] *Stringcolumnname* [*stringformatsuffix*])

**Partitions Parameter**

* *Datetimepartitionformat* : das Format der erforderlichen Verzeichnisstruktur im Ausgabepfad (optional). Wenn die Partitionierung definiert und das Format nicht angegeben wird, lautet der Standardwert "yyyy/mm/dd/hh/mm". Dieses Format basiert auf dem partitionbytimespan-Wert. Wenn Sie z. b. eine Partitionierung durch 1D durchführt, ist die Struktur "yyyy/mm/dd". Wenn Sie eine Partitionierung von 1 h durchführt, wird die Struktur "yyyy/mm/dd/hh" verwendet.
* *Timestampcolumnname* -datetime-Spalte, für die die Tabelle partitioniert ist. Die timestamp-Spalte muss in der Definition des externen Tabellen Schemas vorhanden sein und die Ausgabe der Export Abfrage beim Exportieren in die externe Tabelle enthalten.
* *Partitionbytimespan* : TimeSpan-Literale, nach dem partitioniert werden soll.
* *Stringformatprefix* : ein konstantenzeichenfolgenliteralelement, das Teil des artefaktpfads ist, der vor dem Tabellenwert verkettet wird (optional).
* *Stringformatsuffix* : ein konstantenzeichenfolgenliteralelement, das Teil des artefaktpfads ist, der nach dem Tabellenwert verkettet wird (optional).
* *Stringcolumnname* -Zeichen folgen Spalte, in der die Tabelle partitioniert ist. Die Zeichen folgen Spalte muss in der Definition des externen Tabellen Schemas vorhanden sein.

**Optionale Eigenschaften**:

| Eigenschaft         | type     | Beschreibung       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | Tabellen Ordner                                                                     |
| `docString`      | `string` | Zeichenfolge, die die Tabelle dokumentiert                                                       |
| `compressed`     | `bool`   | Wenn festgelegt, wird angegeben, ob die blobdateien als Dateien komprimiert werden. `.gz`                  |
| `includeHeaders` | `string` | Gibt für CSV-oder TSV-blobdateien an, ob blobzeichen einen Header enthalten.                     |
| `namePrefix`     | `string` | Wenn festgelegt, wird das Präfix der BLOB. Bei Schreibvorgängen werden alle blobvorgänge mit diesem Präfix geschrieben. Bei Lesevorgängen werden nur blobvorgänge mit diesem Präfix gelesen. |
| `fileExtension`  | `string` | Wenn festgelegt, werden Dateierweiterungen der blobdateien angegeben. Beim Schreiben enden blobnamen mit diesem Suffix. Beim Lesen werden nur blobdateien mit dieser Dateierweiterung gelesen.           |
| `encoding`       | `string` | Gibt an, wie der Text codiert wird: `UTF8NoBOM` (Standard) oder `UTF8BOM` .             |

Weitere Informationen zu externen Tabellen Parametern in Abfragen finden Sie unter [artefaktfilterungs-Logik](#artifact-filtering-logic).

> [!NOTE]
> * Wenn die Tabelle vorhanden ist, `.create` schlägt der Befehl mit einem Fehler fehl. Verwenden `.alter` Sie, um vorhandene Tabellen zu ändern. 
> * Das Ändern des Schemas, Formats oder der Partitions Definition einer externen BLOB-Tabelle wird nicht unterstützt. 

Erfordert die [Datenbankbenutzer Berechtigung](../management/access-control/role-based-authorization.md) für `.create` und die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für `.alter` . 
 
**Beispiel** 

Eine nicht partitionierte externe Tabelle. Es wird erwartet, dass alle Artefakte direkt unter den definierten Containern liegen:

```kusto
.create external table ExternalBlob (x:long, s:string) 
kind=blob
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)  
```

Eine externe Tabelle, die nach DateTime partitioniert ist. Artefakte befinden sich in Verzeichnissen im Format "yyyy/mm/dd" unter den definierten Pfaden:

```kusto
.create external table ExternalAdlGen2 (Timestamp:datetime, x:long, s:string) 
kind=adl
partition by bin(Timestamp, 1d)
dataformat=csv
( 
   h@'abfss://filesystem@storageaccount.dfs.core.windows.net/path;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)  
```

Eine externe Tabelle, die nach DateTime mit dem Verzeichnis Format "Year = yyyy/month = mm/Day = DD" partitioniert ist:

```kusto
.create external table ExternalPartitionedBlob (Timestamp:datetime, x:long, s:string) 
kind=blob
partition by format_datetime="'year='yyyy/'month='MM/'day='dd" bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)
```

Eine externe Tabelle mit monatlichen Daten Partitionen und dem Verzeichnis Format "yyyy/mm":

```kusto
.create external table ExternalPartitionedBlob (Timestamp:datetime, x:long, s:string) 
kind=blob
partition by format_datetime="yyyy/MM" bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)
```

Eine externe Tabelle mit zwei Partitionen. Die Verzeichnisstruktur ist die Verkettung beider Partitionen: formatierter CustomerName gefolgt vom Standard DateTime-Format. Beispiel: "CustomerName = Soft Works/2011/11/11":

```kusto
.create external table ExternalMultiplePartitions (Timestamp:datetime, CustomerName:string) 
kind=blob
partition by 
   "CustomerName="CustomerName,
   bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"   
)
```

**Ausgabe**

|TableName|TableType|Ordner|DocString|Eigenschaften|ConnectionStrings|Partitionen|
|---|---|---|---|---|---|---|
|Externalmultiplepartitions|Blob|Externaltables|Docs|{"Format": "CSV", "Compressed": false, "compressionType": NULL, "File Extension": "CSV", "includeheaders": "None", "Encoding": NULL, "NamePrefix": NULL}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat": "CustomerName = {0} ", "ColumnName": "CustomerName", "Ordinal": 0}, partitionby ":" 1,00:00:00 "," ColumnName ":" timestamp "," Ordinal ": 1}]|

### <a name="artifact-filtering-logic"></a>Logik zum Filtern von Artefakten

Beim Abfragen einer externen Tabelle verbessert die Abfrage-Engine die Leistung, indem irrelevante externe Speicher Artefakte (BLOBs) herausgefiltert werden. Der Prozess der Iteration von BLOBs und der Entscheidung, ob ein BLOB verarbeitet werden soll, wird im folgenden beschrieben.

1. Erstellen Sie ein URI-Muster, das eine Stelle darstellt, an der blobspeicher gefunden werden. Anfänglich ist das URI-Muster eine Verbindungs Zeichenfolge, die als Teil der externen Tabellendefinition bereitgestellt wird. Wenn Partitionen definiert sind, werden Sie an das URI-Muster angehängt.
Wenn die Verbindungs Zeichenfolge z. b. lautet `https://storageaccount.blob.core.windows.net/container1` und die DateTime-Partition definiert ist: `partition by format_datetime="yyyy-MM-dd" bin(Timestamp, 1d)` , ist das entsprechende URI-Muster: `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd` , und wir suchen nach BLOB-Speicherorten, die mit diesem Muster übereinstimmen.
Wenn eine zusätzliche Zeichen folgen Partition `"CustomerId" customerId` definiert ist, ist das entsprechende URI-Muster: `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd/CustomerId=*` .

1. Überprüfen Sie für alle direkt unter den erstellten URI-Mustern gefundenen *direkt* -blobzuordnungs:

   * Partitionswerte entsprechen Prädikaten, die in einer Abfrage verwendet werden.
   * Der BLOB-Name beginnt mit `NamePrefix` , wenn eine solche Eigenschaft definiert ist.
   * Der BLOB-Name endet mit `FileExtension` , wenn eine solche Eigenschaft definiert ist.

Sobald alle Bedingungen erfüllt sind, wird das BLOB von der Abfrage-Engine abgerufen und verarbeitet.

### <a name="spark-virtual-columns-support"></a>Unterstützung für virtuelle Spark-Spalten

Beim Exportieren von Daten aus Spark werden Partitions Spalten (die in der-Methode des dataframe-Writers angegeben sind `partitionBy` ) nicht in Datendateien geschrieben. Durch diesen Prozess wird die Daten Duplizierung vermieden, da die Daten bereits in "Ordner" Namen vorhanden sind. Beispielsweise `column1=<value>/column2=<value>/` können von und Spark beim Lesen erkannt werden. Allerdings erfordert Kusto, dass Partitions Spalten in den Daten selbst vorhanden sind. Die Unterstützung für virtuelle Spalten in Kusto ist geplant. Verwenden Sie bis dahin folgende Problem Umgehung: Erstellen Sie beim Exportieren von Daten aus Spark eine Kopie aller Spalten, die von den Daten partitioniert werden, bevor Sie einen dataframe schreiben:

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

Wenn Sie in Kusto eine externe Tabelle definieren, geben Sie Partitions Spalten wie im folgenden Beispiel an:

```kusto
.create external table ExternalSparkTable(a:string, b:datetime) 
kind=blob
partition by 
   "_a="a,
   format_datetime="'_b='yyyyMMdd" bin(b, 1d)
dataformat=parquet
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

## <a name="show-external-table-artifacts"></a>. anzeigen externer Tabellen Artefakte

* Gibt eine Liste aller Artefakte zurück, die verarbeitet werden, wenn eine bestimmte externe Tabelle abgefragt wird.
* Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).

**Syntax:** 

`.show``external` `table` *TableName*`artifacts`

**Ausgabe**

| Output-Parameter | type   | Beschreibung                       |
|------------------|--------|-----------------------------------|
| URI              | Zeichenfolge | URI des externen Speicher Artefakts |

**Beispiele:**

```kusto
.show external table T artifacts
```

**Ausgabe:**

| URI                                                                     |
|-------------------------------------------------------------------------|
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` |

## <a name="create-external-table-mapping"></a>. Erstellen einer externen Tabellen Zuordnung

`.create``external` `table` *Externaltablename* `json` `mapping` *MappingName* *mappinginjsonformat*

Erstellt eine neue Zuordnung. Weitere Informationen finden Sie unter [Daten](./mappings.md#json-mapping)Zuordnungen.

**Beispiel** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Beispielausgabe**

| name     | Variante | Zuordnung                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "ColumnType": "int", "Properties": {"Path": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "ColumnType": "", "Properties": {"Path": "$. ROWGUID"}}] |

## <a name="alter-external-table-mapping"></a>. Alter externer Tabellen Zuordnung

`.alter``external` `table` *Externaltablename* `json` `mapping` *MappingName* *mappinginjsonformat*

Ändert eine vorhandene Zuordnung. 
 
**Beispiel** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Beispielausgabe**

| name     | Variante | Zuordnung                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "ColumnType": "", "Properties": {"Path": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "ColumnType": "", "Properties": {"Path": "$. ROWGUID"}}] |

## <a name="show-external-table-mappings"></a>. Zuordnungen externer Tabellen anzeigen

`.show``external` `table` *Externaltablename* `json` `mapping` *MappingName* 

`.show``external` `table` *Externaltablename* `json``mappings`

Zeigt die Zuordnungen an (alle oder die durch den Namen angegebenen).
 
**Beispiel** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**Beispielausgabe**

| name     | Variante | Zuordnung                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "ColumnType": "", "Properties": {"Path": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "ColumnType": "", "Properties": {"Path": "$. ROWGUID"}}] |

## <a name="drop-external-table-mapping"></a>. Löschen der Zuordnung externer Tabellen

`.drop``external` `table` *Externaltablename* `json` `mapping` *MappingName* 

Löscht die Zuordnung aus der Datenbank.
 
**Beispiel** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```
