---
title: 'Verwaltung externer Tabellen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Verwaltung externer Tabellen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: c52f0649531678e31310f5a1f4bfb97f99f15857
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741940"
---
# <a name="external-table-management"></a>Verwaltung externer Tabellen

Eine Übersicht über externe Tabellen finden Sie unter [externe Tabellen](../query/schema-entities/externaltables.md) . 

## <a name="common-external-tables-control-commands"></a>Allgemeine Befehle zum Steuern externer Tabellen

Die folgenden Befehle sind für _jede_ externe Tabelle (eines beliebigen Typs) relevant.

### <a name="show-external-tables"></a>. anzeigen externer Tabellen

* Gibt alle externen Tabellen in der Datenbank zurück (oder eine bestimmte externe Tabelle).
* Erfordert die [Datenbank-Monitor-Berechtigung](../management/access-control/role-based-authorization.md).

**Syntax:** 

`.show` `external` `tables`

`.show``external` `table` *TableName*

**Ausgabe**

| Output-Parameter | Typ   | Beschreibung                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | string | Name der externen Tabelle                                             |
| TableType        | string | Typ externer Tabelle                                              |
| Ordner           | string | Tabellen Ordner                                                     |
| DocString        | string | Zeichenfolge, die die Tabelle dokumentiert                                       |
| Eigenschaften       | string | JSON-serialisierte Eigenschaften der Tabelle (spezifisch für den Typ der Tabelle) |


**Beispiele:**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | Ordner         | DocString | Eigenschaften |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | Externaltables | Docs      | {}         |


### <a name="show-external-table-schema"></a>. Anzeigen eines externen Tabellen Schemas

* Gibt das Schema der externen Tabelle als JSON oder CSL zurück. 
* Erfordert die [Datenbank-Monitor-Berechtigung](../management/access-control/role-based-authorization.md).

**Syntax:** 

`.show``external` `as` *TableName* `schema` `csl`TableName (`json`) |  `table`

`.show``external` `table` *TableName*`cslschema`

**Ausgabe**

| Output-Parameter | Typ   | Beschreibung                        |
|------------------|--------|------------------------------------|
| TableName        | string | Name der externen Tabelle            |
| Schema           | string | Das Tabellen Schema in einem JSON-Format. |
| DatabaseName     | string | Datenbankname der Tabelle             |
| Ordner           | string | Tabellen Ordner                    |
| DocString        | string | Zeichenfolge, die die Tabelle dokumentiert      |

**Beispiele:**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**Ausgabe:**

*JSON*

| TableName | Schema    | DatabaseName | Ordner         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"Name": "externalblob",<br>"Folder": "externaltables",<br>"DocString": "docs",<br>"Orderedcolumns": [{"Name": "x", "Type": "System. Int64", "csltype": "Long", "DocString": ""}, {"Name": "s", "Type": "System. String", "csltype": "String", "DocString": ""}]} | DB           | Externaltables | Docs      |


*CSL*

| TableName | Schema          | DatabaseName | Ordner         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:Long, s:Zeichenfolge | DB           | Externaltables | Docs      |

### <a name="drop-external-table"></a>. externe Tabelle löschen

* Löscht eine externe Tabelle. 
* Die Definition der externen Tabelle kann nach diesem Vorgang nicht wieder hergestellt werden.
* Erfordert die [Administrator Berechtigung](../management/access-control/role-based-authorization.md)für die Datenbank.

**Syntax:**  

`.drop``external` `table` *TableName*

**Ausgabe**

Gibt die Eigenschaften der gelöschten Tabelle zurück. Weitere Informationen finden Sie unter [. anzeigen externer Tabellen](#show-external-tables).

**Beispiele:**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | Ordner         | DocString | Schema       | Eigenschaften |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | Externaltables | Docs      | [{"Name": "x", "csltype": "Long"},<br> {"Name": "s", "csltype": "String"}] | {}         |

## <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Externe Tabellen in Azure Storage oder Azure Data Lake

Mit dem folgenden Befehl wird beschrieben, wie eine externe Tabelle erstellt wird. Die Tabelle kann sich in Azure BLOB Storage, Azure Data Lake Store Gen1 oder Azure Data Lake Store Gen2 befinden. 
[Speicher Verbindungs](../api/connection-strings/storage.md) Zeichenfolgen beschreiben das Erstellen der Verbindungs Zeichenfolge für jede dieser Optionen. 

### <a name="create-or-alter-external-table"></a>. Create oder. Alter externe Tabelle

**Syntax**

`.create` | (`.alter`) `external` *TableName* (*Schema)* `table`  
`kind` `=` (`blob` | `adl`)  
[`partition` `by` *Partition* [`,` ...]]  
`dataformat``=` *Format*  
`(`  
*Storageconnectionstring* [`,` ...]  
`)`  
[`with` `(`[`docstring` `=` *value*`,` *property_name* *Documentation* `=` *FolderName*Dokumentation] [`,` `folder` FolderName], property_name Wert... `=` `)`]

Erstellt oder ändert eine neue externe Tabelle in der Datenbank, in der der Befehl ausgeführt wird.

**Parameters**

* *TableName* -Name der externen Tabelle. Muss den Regeln für [Entitäts Namen](../query/schema-entities/entity-names.md)folgen. Eine externe Tabelle kann nicht den gleichen Namen wie eine reguläre Tabelle in derselben Datenbank aufweisen.
* *Schema* -externes Datenschema im Format: `ColumnName:ColumnType[, ColumnName:ColumnType ...]`. Wenn das externe Datenschema unbekannt ist, verwenden Sie das [infer_storage_schema](../query/inferstorageschemaplugin.md) -Plug-in, das das Schema basierend auf dem Inhalt externer Dateien ableiten kann.
* *Partition* : eine oder mehrere Partitions Definitionen (optional). Siehe Partitions Syntax weiter unten.
* *Format* : das Datenformat. Alle Erfassungs [Formate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) werden für Abfragen unterstützt. Die Verwendung einer externen Tabelle für das [Export Szenario](data-export/export-data-to-an-external-table.md) ist auf die folgenden `CSV`Formate `TSV`beschränkt `JSON`: `Parquet`,,,.
* *Storageconnectionstring* : ein oder mehrere Pfade zu Azure BLOB Storage BLOB-Containern oder Azure Data Lake Store Dateisystemen (virtuelle Verzeichnisse oder Ordner), einschließlich der Anmelde Informationen. Weitere Informationen finden Sie unter [Speicher Verbindungs](../api/connection-strings/storage.md) Zeichenfolgen. Es wird dringend empfohlen, mehr als ein einzelnes Speicherkonto bereitzustellen, um eine Speicher Drosselung zu vermeiden, wenn Sie große Datenmengen in die externe Tabelle [exportieren](data-export/export-data-to-an-external-table.md) . Beim Export werden die Schreibvorgänge zwischen allen bereitgestellten Konten verteilt. 

**Partitions Syntax**

[`format_datetime =` *Datetimepartitionformat*] `bin(` *Timestampcolumnname*, *partitionbytimespan*`)`  
|   
[*Stringformatprefix*] *Stringcolumnname* [*stringformatsuffix*])

**Partitions Parameter**

* *Datetimepartitionformat* : das Format der erforderlichen Verzeichnisstruktur im Ausgabepfad (optional). Wenn die Partitionierung definiert und das Format nicht angegeben wird, lautet der Standardwert "yyyy/mm/dd/hh/mm", basierend auf der partitionbytimespan. Wenn Sie z. b. eine Partitionierung durch 1D durchführt, ist die Struktur "yyyy/mm/dd". Wenn Sie eine Partitionierung von 1 Stunde durchführt, wird die Struktur "yyyy/mm/dd/hh" verwendet.
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
| `compressed`     | `bool`   | Wenn festgelegt, wird angegeben, ob die blobdateien als `.gz` Dateien komprimiert werden.                  |
| `includeHeaders` | `string` | Gibt für CSV-oder TSV-blobdateien an, ob blobzeichen einen Header enthalten.                     |
| `namePrefix`     | `string` | Wenn festgelegt, wird das Präfix der BLOB. Bei Schreibvorgängen werden alle blobvorgänge mit diesem Präfix geschrieben. Bei Lesevorgängen werden nur blobvorgänge mit diesem Präfix gelesen. |
| `fileExtension`  | `string` | Wenn festgelegt, werden Dateierweiterungen der blobdateien angegeben. Beim Schreiben enden blobnamen mit diesem Suffix. Beim Lesen werden nur blobdateien mit dieser Dateierweiterung gelesen.           |
| `encoding`       | `string` | Gibt an, wie der Text codiert wird: `UTF8NoBOM` ( `UTF8BOM`Standard) oder.             |

Weitere Informationen zu externen Tabellen Parametern in Abfragen finden Sie unter [artefaktfilterungs-Logik](#artifact-filtering-logic).

> [!NOTE]
> * Wenn die Tabelle vorhanden ist `.create` , schlägt der Befehl mit einem Fehler fehl. Verwenden `.alter` Sie, um vorhandene Tabellen zu ändern. 
> * Das Ändern des Schemas, Formats oder der Partitions Definition einer externen BLOB-Tabelle wird nicht unterstützt. 

Erfordert die [Datenbankbenutzer Berechtigung](../management/access-control/role-based-authorization.md) für `.create` und die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für. `.alter` 
 
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
|Externalmultiplepartitions|Blob|Externaltables|Docs|{"Format": "CSV", "Compressed": false, "compressionType": NULL, "File Extension": "CSV", "includeheaders": "None", "Encoding": NULL, "NamePrefix": NULL}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat": "CustomerName ={0}", "ColumnName": "CustomerName", "Ordinal": 0}, partitionby ":" 1,00:00:00 "," ColumnName ":" timestamp "," Ordinal ": 1}]|

#### <a name="artifact-filtering-logic"></a>Logik zum Filtern von Artefakten

Beim Abfragen einer externen Tabelle filtert die Abfrage-Engine irrelevante externe Speicher Artefakte (BLOBs) heraus, um die Abfrageleistung zu verbessern. Der Prozess der Iteration von BLOBs und die Entscheidung, ob ein BLOB verarbeitet werden soll, finden Sie unten.

1. Erstellen Sie ein URI-Muster, das eine Stelle darstellt, an der blobspeicher gefunden werden. Anfänglich ist das URI-Muster eine Verbindungs Zeichenfolge, die als Teil der externen Tabellendefinition bereitgestellt wird. Wenn Partitionen definiert sind, werden Sie an das URI-Muster angehängt.
Wenn die Verbindungs Zeichenfolge z. b `https://storageaccount.blob.core.windows.net/container1` . lautet und die DateTime-Partition `partition by format_datetime="yyyy-MM-dd" bin(Timestamp, 1d)`definiert ist:, ist das entsprechende URI- `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd`Muster:, und wir suchen nach BLOB-Speicherorten, die mit diesem Muster übereinstimmen.
Wenn eine zusätzliche Zeichen folgen Partition `"CustomerId" customerId` definiert ist, lautet das entsprechende URI-Muster wie `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd/CustomerId=*`folgt: usw.

2. Überprüfen Sie für alle direkt unter den erstellten URI-Mustern gefundenen *direkt* -blobzuordnungs:

 * Partitionswerte entsprechen Prädikaten, die in einer Abfrage verwendet werden.
 * Der BLOB-Name `NamePrefix`beginnt mit, wenn eine solche Eigenschaft definiert ist.
 * Der BLOB-Name `FileExtension`endet mit, wenn eine solche Eigenschaft definiert ist.

Sobald alle Bedingungen erfüllt sind, wird das BLOB von der Abfrage-Engine abgerufen und verarbeitet.

#### <a name="spark-virtual-columns-support"></a>Unterstützung für virtuelle Spark-Spalten

Beim Exportieren von Daten aus Spark werden Partitions Spalten (die in der-Methode des dataframe `partitionBy` -Writers angegeben sind) nicht in Datendateien geschrieben. Dies erfolgt, um die Daten Duplizierung zu vermeiden, da die Daten bereits in "Ordnernamen" ( `column1=<value>/column2=<value>/`z. b.) vorhanden sind und Spark Sie beim Lesen erkennen kann. Allerdings erfordert Kusto, dass Partitions Spalten in den Daten selbst vorhanden sind. Die Unterstützung für virtuelle Spalten in Kusto ist geplant. Verwenden Sie bis dahin folgende Problem Umgehung: Erstellen Sie beim Exportieren von Daten aus Spark eine Kopie aller Spalten, die von den Daten partitioniert werden, bevor Sie einen dataframe schreiben:

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

Wenn Sie eine externe Tabelle in Kusto definieren, geben Sie Partitions Spalten wie im folgenden Beispiel an:

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

### <a name="show-external-table-artifacts"></a>. anzeigen externer Tabellen Artefakte

* Gibt eine Liste aller Artefakte zurück, die verarbeitet werden, wenn eine bestimmte externe Tabelle abgefragt wird.
* Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).

**Syntax:** 

`.show``external` `table` *TableName*`artifacts`

**Ausgabe**

| Output-Parameter | Typ   | Beschreibung                       |
|------------------|--------|-----------------------------------|
| URI              | string | URI des externen Speicher Artefakts |

**Beispiele:**

```kusto
.show external table T artifacts
```

**Ausgabe:**

| URI                                                                     |
|-------------------------------------------------------------------------|
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` |

### <a name="create-external-table-mapping"></a>. Erstellen einer externen Tabellen Zuordnung

`.create``external` `json` `mapping` *MappingName* *ExternalTableName* Externaltablename MappingName *mappinginjsonformat* `table`

Erstellt eine neue Zuordnung. Weitere Informationen finden Sie unter [Daten](./mappings.md#json-mapping)Zuordnungen.

**Beispiel** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Beispielausgabe**

| name     | Variante | Zuordnung                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "ColumnType": "int", "Properties": {"Path": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "ColumnType": "", "Properties": {"Path": "$. ROWGUID"}}] |

### <a name="alter-external-table-mapping"></a>. Alter externer Tabellen Zuordnung

`.alter``external` `json` `mapping` *MappingName* *ExternalTableName* Externaltablename MappingName *mappinginjsonformat* `table`

Ändert eine vorhandene Zuordnung. 
 
**Beispiel** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Beispielausgabe**

| name     | Variante | Zuordnung                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "ColumnType": "", "Properties": {"Path": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "ColumnType": "", "Properties": {"Path": "$. ROWGUID"}}] |

### <a name="show-external-table-mappings"></a>. Zuordnungen externer Tabellen anzeigen

`.show``external` `json` `mapping` *MappingName* *ExternalTableName* Externaltablename MappingName `table` 

`.show``external` `json` Externaltablename *ExternalTableName* `table``mappings`

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

### <a name="drop-external-table-mapping"></a>. Löschen der Zuordnung externer Tabellen

`.drop``external` `json` `mapping` *MappingName* *ExternalTableName* Externaltablename MappingName `table` 

Löscht die Zuordnung aus der Datenbank.
 
**Beispiel** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```

## <a name="external-sql-table"></a>Externe SQL-Tabelle

### <a name="create-or-alter-external-sql-table"></a>. Create oder Alter externer SQL-Tabelle

Erstellt oder ändert eine externe SQL-Tabelle in der Datenbank, in der der Befehl ausgeführt wird.  

**Syntax**

`.create` | (`.alter`) `external` TableName ([columnName: ColumnType],...) *TableName* `table`  
`kind` `=` `sql`  
`table``=` *Sqltablename*  
`(`*Sqlserverconnectionstring*`)`  
[`with` `(`[`docstring` `=` *value*`,` *property_name* *Documentation* `=` *FolderName*Dokumentation] [`,` `folder` FolderName], property_name Wert... `=` `)`]


**Metern**

* *TableName* -Name der externen Tabelle. Muss den Regeln für [Entitäts Namen](../query/schema-entities/entity-names.md)folgen. Eine externe Tabelle kann nicht den gleichen Namen wie eine reguläre Tabelle in derselben Datenbank aufweisen.
* *Sqltablename* : der Name der SQL-Tabelle.
* *Sqlserverconnectionstring* : die Verbindungs Zeichenfolge für den SQL-Server. Dabei kann es sich um eine der folgenden Methoden handeln: 
    * **Integrierte Aad** -Authentifizierung`Authentication="Active Directory Integrated"`(): der Benutzer oder die Anwendung authentifiziert sich über Aad bei Kusto, und das gleiche Token wird dann verwendet, um auf den SQL Server Netzwerk Endpunkt zuzugreifen.
    * **Benutzernamen-/Kennwort-Authentifizierung** (`User ID=...; Password=...;`). Wenn die externe Tabelle für den [fortlaufenden Export](data-export/continuous-data-export.md)verwendet wird, muss die Authentifizierung mithilfe dieser Methode durchgeführt werden. 

> [!WARNING]
> Verbindungs Zeichenfolgen und Abfragen, die vertrauliche Informationen enthalten, sollten verdeckt werden, damit Sie bei jeder Kusto-Ablauf Verfolgung ausgelassen werden. Weitere Informationen finden Sie unter verborgene [Zeichen folgen Literale](../query/scalar-data-types/string.md#obfuscated-string-literals) .

**Optionale Eigenschaften**
| Eigenschaft            | type            | Beschreibung                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | Der Ordner der Tabelle.                  |
| `docString`         | `string`        | Eine Zeichenfolge, die die Tabelle dokumentiert.      |
| `firetriggers`      | `true`/`false`  | Wenn `true`, wird das Zielsystem angewiesen, INSERT-Trigger auszulösen, die für die SQL-Tabelle definiert sind. Der Standardwert ist `false`. (Weitere Informationen finden Sie unter [Bulk Insert](https://msdn.microsoft.com/library/ms188365.aspx) und [System. Data. SqlClient. SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)). |
| `createifnotexists` | `true`/ `false` | Gibt `true`an, dass die SQL-Ziel Tabelle erstellt wird, wenn Sie nicht bereits vorhanden ist. in `primarykey` diesem Fall muss die-Eigenschaft bereitgestellt werden, um die Ergebnisspalte anzugeben, bei der es sich um den Primärschlüssel handelt. Der Standardwert ist `false`.  |
| `primarykey`        | `string`        | Wenn `createifnotexists` den `true`Wert hat, wird der Name der Spalte im Ergebnis angegeben, die als Primärschlüssel der SQL-Tabelle verwendet wird, wenn Sie mit diesem Befehl erstellt wird.                  |

> [!NOTE]
> * Wenn die Tabelle vorhanden ist, `.create` tritt bei dem Befehl ein Fehler auf. Verwenden `.alter` Sie, um vorhandene Tabellen zu ändern. 
> * Das Ändern des Schemas oder Formats einer externen SQL-Tabelle wird nicht unterstützt. 

Erfordert die [Datenbankbenutzer Berechtigung](../management/access-control/role-based-authorization.md) für `.create` und die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für. `.alter` 
 
**Beispiel** 

```kusto
.create external table ExternalSql (x:long, s:string) 
kind=sql
table=MySqlTable
( 
   h@'Server=tcp:myserver.database.windows.net,1433;Authentication=Active Directory Integrated;Initial Catalog=mydatabase;'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables", 
   createifnotexists = true,
   primarykey = x,
   firetriggers=true
)  
```

**Ausgabe**

| TableName   | TableType | Ordner         | DocString | Eigenschaften                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| Externalsql | Sql       | Externaltables | Docs      | {<br>  "Targetentitykind": "sqltable",<br>  "Targetentityname": "mysqltable",<br>  "Targetentityconnectionstring": "Server = TCP:myserver. Database. Windows. net, 1433; Authentication = Active Directory integriert; anfangs Katalog = MyDatabase; ",<br>  "FireTriggers": true,<br>  "Kreateif NotExists": true,<br>  "PrimaryKey": "x"<br>} |

### <a name="querying-an-external-table-of-type-sql"></a>Abfragen einer externen Tabelle vom Typ SQL 
Similarl für andere Typen externer Tabellen, das Abfragen einer externen SQL-Tabelle wird unterstützt. Weitere Informationen finden Sie unter [Abfragen externer Tabellen](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data). Beachten Sie, dass die Abfrage Implementierung der externen SQL-Tabelle in der SQL-Tabelle eine vollständige SELECT *-Abfrage ausführt (bzw. relevante Spalten auswählen), während der Rest der Abfrage auf der Kusto-Seite ausgeführt wird. Sehen Sie sich die folgende externe Tabellen Abfrage an: 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto führt eine Abfrage vom Typ "Select * from table" für die SQL-Datenbank aus, gefolgt von einer Anzahl auf Kusto-Seite. In solchen Fällen wird erwartet, dass die Leistung besser ist, wenn Sie in T-SQL direkt geschrieben wird ("SELECT count (1) from table") und mit dem [sql_request-Plug](../query/sqlrequestplugin.md)-in ausgeführt wird, anstatt die externe Tabellen Funktion zu verwenden. Ebenso werden Filter nicht an die SQL-Abfrage übermittelt.  
Es wird empfohlen, die externe Tabelle zum Abfragen der SQL-Tabelle zu verwenden, wenn die Abfrage das Lesen der gesamten Tabelle (oder relevanter Spalten) für die weitere Ausführung auf Kusto-Seite erfordert. Wenn eine SQL-Abfrage in T-SQL erheblich optimiert werden kann, verwenden Sie das [sql_request-Plug](../query/sqlrequestplugin.md)-in.
