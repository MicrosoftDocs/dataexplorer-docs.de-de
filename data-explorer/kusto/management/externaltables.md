---
title: Externe Tabellenverwaltung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Verwaltung der externen Tabelle in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 680a4e25d6b478fe171aa3296de81c0106417877
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744713"
---
# <a name="external-table-management"></a>Externe Tabellenverwaltung

Eine Übersicht über externe Tabellen finden Sie in [externen Tabellen.](../query/schema-entities/externaltables.md) 

## <a name="common-external-tables-control-commands"></a>Allgemeine Steuerbefehle für externe Tabellen

Die folgenden Befehle sind für _jede_ externe Tabelle (jeden Typs) relevant.

### <a name="show-external-tables"></a>.anzeigen externe Tabellen

* Gibt alle externen Tabellen in der Datenbank (oder eine bestimmte externe Tabelle) zurück.
* Erfordert [datenbanküberwachte Berechtigung](../management/access-control/role-based-authorization.md).

**Syntax:** 

`.show` `external` `tables`

`.show``external` `table` *TableName*

**Ausgabe**

| Ausgabeparameter | type   | BESCHREIBUNG                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | Zeichenfolge | Name der externen Tabelle                                             |
| TableType        | Zeichenfolge | Typ der externen Tabelle                                              |
| Ordner           | Zeichenfolge | Ordner der Tabelle                                                     |
| DocString        | Zeichenfolge | Zeichenfolge, die die Tabelle dokumentiert                                       |
| Eigenschaften       | Zeichenfolge | Die serialisierten JSON-Eigenschaften der Tabelle (spezifisch für den Tabellentyp) |


**Beispiele:**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | Ordner         | DocString | Eigenschaften |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | ExternalTables | Docs      | {}         |


### <a name="show-external-table-schema"></a>.Anzeigen des externen Tabellenschemas

* Gibt das Schema der externen Tabelle als JSON oder CSL zurück. 
* Erfordert [datenbanküberwachte Berechtigung](../management/access-control/role-based-authorization.md).

**Syntax:** 

`.show``external` `as` *TableName* `json`Tabellenname | (`csl`) `table` `schema`

`.show``external` `table` *TableName*`cslschema`

**Ausgabe**

| Ausgabeparameter | type   | BESCHREIBUNG                        |
|------------------|--------|------------------------------------|
| TableName        | Zeichenfolge | Name der externen Tabelle            |
| Schema           | Zeichenfolge | Das Tabellenschema im JSON-Format |
| DatabaseName     | Zeichenfolge | Datenbankname der Tabelle             |
| Ordner           | Zeichenfolge | Ordner der Tabelle                    |
| DocString        | Zeichenfolge | Zeichenfolge, die die Tabelle dokumentiert      |

**Beispiele:**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**Ausgabe:**

*Json:*

| TableName | Schema    | DatabaseName | Ordner         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | "Name":"ExternalBlob",<br>"Folder":"ExternalTables",<br>"DocString":"Docs",<br>"OrderedColumns":['Name":"x","Type":"System.Int64","CslType":"long","DocString":"","Name":"s","Type":"System.String","CslType":"string","DocString":"""""" | DB           | ExternalTables | Docs      |


*Csl:*

| TableName | Schema          | DatabaseName | Ordner         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:long,s:string | DB           | ExternalTables | Docs      |

### <a name="drop-external-table"></a>.drop externe Tabelle

* Löscht eine externe Tabelle 
* Die externe Tabellendefinition kann nach diesem Vorgang nicht wiederhergestellt werden.
* Erfordert [Datenbankadministratorberechtigung](../management/access-control/role-based-authorization.md).

**Syntax:**  

`.drop``external` `table` *TableName*

**Ausgabe**

Gibt die Eigenschaften der gelöschten Tabelle zurück. Siehe [.show externe Tabellen](#show-external-tables).

**Beispiele:**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | Ordner         | DocString | Schema       | Eigenschaften |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | ExternalTables | Docs      | "Name": "x", "CslType": "long",<br> - "Name": "s", "CslType": "string" | {}         |

## <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Externe Tabellen in Azure Storage oder Azure Data Lake

Der folgende Befehl beschreibt, wie eine externe Tabelle erstellt wird. Die Tabelle kann sich in Azure Blob Storage, Azure Data Lake Store Gen1 oder Azure Data Lake Store Gen2 befinden. 
[Speicherverbindungszeichenfolgen](../api/connection-strings/storage.md) beschreiben das Erstellen der Verbindungszeichenfolge für jede dieser Optionen. 

### <a name="create-or-alter-external-table"></a>.create oder .alter externe Tabelle

**Syntax**

`.create` | (`.alter` `external` ) `table` *TableName* (*Schema*)  
`kind` `=` (`blob` | `adl`)  
[`partition` `by` *Partition* Partition`,` [ ....]]  
`dataformat``=` *Format*  
`(`  
*StorageConnectionString* `,` [ ...]  
`)`  
[`with` `(``docstring` `=` [ *Dokumentation*`,` `folder` `=` ] [ *Ordnername*], *property_name* `=` *Wert*`,`... `)`]

Erstellt oder ändert eine neue externe Tabelle in der Datenbank, in der der Befehl ausgeführt wird.

**Parameter**

* *TableName* - Externer Tabellenname. Muss den Regeln für [Entitätsnamen](../query/schema-entities/entity-names.md)folgen. Eine externe Tabelle kann nicht denselben Namen wie eine reguläre Tabelle in derselben Datenbank haben.
* *Schema* - Externes Datenschema im Format: `ColumnName:ColumnType[, ColumnName:ColumnType ...]`. Wenn das externe Datenschema unbekannt [infer_storage_schema](../query/inferstorageschemaplugin.md) ist, verwenden Sie das infer_storage_schema-Plug-In, das das Schema basierend auf externen Dateiinhalten ableiten kann.
* *Partition* - Eine oder mehrere Partitionsdefinitionen (optional). Siehe Partitionssyntax unten.
* *Format* - Das Datenformat. Jedes der [Aufnahmeformate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) wird für Abfragen unterstützt. Die Verwendung einer externen Tabelle für das `CSV` `TSV` [Exportszenario](data-export/export-data-to-an-external-table.md) ist auf die folgenden Formate beschränkt: , , `JSON`, `Parquet`.
* *StorageConnectionString* - Ein oder mehrere Pfade zu Azure Blob Storage-Blobcontainern oder Azure Data Lake Store-Dateisystemen (virtuelle Verzeichnisse oder Ordner), einschließlich Anmeldeinformationen. Weitere Informationen finden Sie unter [Speicherverbindungszeichenfolgen.](../api/connection-strings/storage.md) Es wird dringend empfohlen, mehr als ein einzelnes Speicherkonto bereitzustellen, um Speichereinschränkungen zu vermeiden, wenn große Datenmengen in die externe Tabelle [exportiert](data-export/export-data-to-an-external-table.md) werden. Beim Export werden die Schreibvorgänge auf alle bereitgestellten Konten verteilt. 

**Partitionssyntax**

[`format_datetime =` *DateTimePartitionFormat*] `bin(` *TimestampColumnName*, *PartitionByTimeSpan*`)`  
|   
[*StringFormatPrefix*] *StringColumnName* [*StringFormatSuffix*])

**Partitionsparameter**

* *DateTimePartitionFormat* - Das Format der erforderlichen Verzeichnisstruktur im Ausgabepfad (optional). Wenn Partitionierung definiert ist und das Format nicht angegeben ist, ist der Standardwert "yyyy/MM/dd/HH/mm", basierend auf PartitionByTimeSpan. Wenn Sie z. B. durch 1d partitionieren, wird die Struktur "yyyy/MM/dd" sein. Wenn Sie die Partitionierung durch 1h, Struktur wird "yyyy/MM/dd/HH".
* *TimestampColumnName* - Datetime-Spalte, in der die Tabelle partitioniert ist. Die Zeitstempelspalte muss in der externen Tabellenschemadefinition und Ausgabe der Exportabfrage beim Exportieren in die externe Tabelle vorhanden sein.
* *PartitionByTimeSpan* - Timespan-Literal, um das partitioniert werden soll.
* *StringFormatPrefix* - Ein konstantes Zeichenfolgenliteral, das Teil des Artefaktpfads ist und vor dem Tabellenwert verkettet ist (optional).
* *StringFormatSuffix* - Ein konstantes Zeichenfolgenliteral, das Teil des Artefaktpfads ist und nach dem Tabellenwert verkettet ist (optional).
* *StringColumnName* - Zeichenfolgenspalte, für die die Tabelle partitioniert ist. Die Zeichenfolgenspalte muss in der externen Tabellenschemadefinition vorhanden sein.

**Optionale Eigenschaften**:

| Eigenschaft         | type     | BESCHREIBUNG       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | Ordner der Tabelle                                                                     |
| `docString`      | `string` | Zeichenfolge, die die Tabelle dokumentiert                                                       |
| `compressed`     | `bool`   | Wenn gesetzt, gibt an, ob `.gz` die Blobs als Dateien komprimiert werden                  |
| `includeHeaders` | `string` | Gibt für CSV- oder TSV-Blobs an, ob Blobs einen Header enthalten                     |
| `namePrefix`     | `string` | Wenn gesetzt, gibt das Präfix der Blobs an. Bei Schreibvorgängen werden alle Blobs mit diesem Präfix geschrieben. Bei Lesevorgängen werden nur Blobs mit diesem Präfix gelesen. |
| `fileExtension`  | `string` | Wenn gesetzt, gibt Dateierweiterungen der Blobs an. Beim Schreiben enden Blobsnamen mit diesem Suffix. Beim Lesen werden nur Blobs mit dieser Dateierweiterung gelesen.           |
| `encoding`       | `string` | Gibt an, wie der `UTF8NoBOM` Text codiert `UTF8BOM`wird: (Standard) oder .             |

> [!NOTE]
> * Wenn die Tabelle `.create` vorhanden ist, schlägt der Befehl mit einem Fehler fehl. Verwenden `.alter` Sie diese Datei zum Ändern vorhandener Tabellen. 
> * Das Ändern des Schemas, des Formats oder der Partitionsdefinition einer externen Blobtabelle wird nicht unterstützt. 

Erfordert [Datenbankbenutzerberechtigung](../management/access-control/role-based-authorization.md) für `.create` und `.alter` [Tabellenadministratorberechtigung](../management/access-control/role-based-authorization.md) für . 
 
**Beispiel** 

Eine nicht partitionierte externe Tabelle. Es wird erwartet, dass sich alle Artefakte direkt unter den definierten Containern befinden:

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
   folder = "ExternalTables",
   namePrefix="Prefix"
)  
```

Eine externe Tabelle, die nach dateTime partitioniert wird. Artefakte befinden sich in Verzeichnissen im "yyyy/MM/dd"-Format unter den definierten Pfaden:

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
   folder = "ExternalTables",
   namePrefix="Prefix"
)  
```

Eine externe Tabelle, die nach dateTime partitioniert ist, mit dem Verzeichnisformat "year=yyyy/month=MM/day=dd":

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
   folder = "ExternalTables",
   namePrefix="Prefix"
)
```

Eine externe Tabelle mit monatlichen Datenpartitionen und einem Verzeichnisformat von "yyyy/MM":

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
   folder = "ExternalTables",
   namePrefix="Prefix"
)
```

Eine externe Tabelle mit zwei Partitionen. Die Verzeichnisstruktur ist die Verkettung beider Partitionen: formatiert CustomerName gefolgt von Standard-dateTime-Format. Beispiel: "CustomerName=softworks/2011/11/11":

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
|ExternalMultiplePartitions|Blob|ExternalTables|Docs|'Format":"Csv","Compressed":false,"CompressionType":null,"FileExtension":"csv","IncludeHeaders":"None","Encoding":null,"NamePrefix":null|["https://storageaccount.blob.core.windows.net/container1;*******"]}|['StringFormat":"CustomerName={0}","ColumnName":"CustomerName","Ordinal":0',PartitionBy":"1.00:00:00","ColumnName":"Timestamp","Ordinal":1']|

#### <a name="spark-virtual-columns-support"></a>Unterstützung virtueller Spark-Säulen

Wenn Daten aus Spark exportiert werden, werden Partitionsspalten `partitionBy` (die in der Dataframe Writer-Methode angegeben sind) nicht in Datendateien geschrieben. Dies geschieht, um Datenduplizierung zu vermeiden, da die `column1=<value>/column2=<value>/`Daten, die bereits in "Ordner"-Namen vorhanden sind (z. B. ), und Spark sie beim Lesen erkennen kann. Kusto erfordert jedoch, dass Partitionsspalten in den Daten selbst vorhanden sind. Unterstützung für virtuelle Spalten in Kusto ist geplant. Verwenden Sie bis dahin die folgende Problemumgehung: Erstellen Sie beim Exportieren von Daten aus Spark eine Kopie aller Spalten, von denen Daten partitioniert werden, bevor Sie einen Datenrahmen schreiben:

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

Wenn Sie eine externe Tabelle in Kusto definieren, geben Sie Partitionsspalten wie im folgenden Beispiel an:

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

### <a name="show-external-table-artifacts"></a>.anzeigen externe Tabellenartefakte

* Gibt eine Liste aller Artefakte zurück, die beim Abfragen einer bestimmten externen Tabelle verarbeitet werden.
* Erfordert [Datenbankbenutzerberechtigungen](../management/access-control/role-based-authorization.md).

**Syntax:** 

`.show``external` `table` *TableName*`artifacts`

**Ausgabe**

| Ausgabeparameter | type   | BESCHREIBUNG                       |
|------------------|--------|-----------------------------------|
| Uri              | Zeichenfolge | URI des externen Speicherartefakts |

**Beispiele:**

```kusto
.show external table T artifacts
```

**Ausgabe:**

| Uri                                                                     |
|-------------------------------------------------------------------------|
| https://storageaccount.blob.core.windows.net/container1/folder/file.csv |

### <a name="create-external-table-mapping"></a>.create externe Tabellenzuordnung

`.create``external` `mapping` *MappingName* *ExternalTableName* `json` *MappingInJsonFormat* ExternalTableName MappingName MappingInJsonFormat `table`

Erstellt eine neue Zuordnung. Weitere Informationen finden Sie unter [Datenzuordnungen](./mappings.md#json-mapping).

**Beispiel** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Beispielausgabe**

| Name     | Variante | Zuordnung                                                           |
|----------|------|-------------------------------------------------------------------|
| Mapping1 | JSON | [''ColumnName":"rownumber",'ColumnType":"int",'Properties''''Path":"'.rownumber'',''ColumnName":"rowguid","ColumnType":"",'Properties'''Path":"'.rowguid'''] |

### <a name="alter-external-table-mapping"></a>.ändern externe Tabellenzuordnung

`.alter``external` `mapping` *MappingName* *ExternalTableName* `json` *MappingInJsonFormat* ExternalTableName MappingName MappingInJsonFormat `table`

Ändert eine vorhandene Zuordnung. 
 
**Beispiel** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Beispielausgabe**

| Name     | Variante | Zuordnung                                                                |
|----------|------|------------------------------------------------------------------------|
| Mapping1 | JSON | [''ColumnName":"rownumber",'ColumnType":"",'Properties'''Path":"'.rownumber'','ColumnName":"rowguid","ColumnType":"","Properties":''Path":"'.rowguid'''] |

### <a name="show-external-table-mappings"></a>.Anzeigen externer Tabellenzuordnungen

`.show``external` `mapping` *ExternalTableName* `json` ExternalTableName *MappingName* `table` 

`.show``external` *ExternalTableName* `json` ExternalTableName `table``mappings`

Zeigen Sie die Zuordnungen (alle oder die nach Namen angegebene).
 
**Beispiel** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**Beispielausgabe**

| Name     | Variante | Zuordnung                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| Mapping1 | JSON | [''ColumnName":"rownumber",'ColumnType":"",'Properties'''Path":"'.rownumber'','ColumnName":"rowguid","ColumnType":"","Properties":''Path":"'.rowguid'''] |

### <a name="drop-external-table-mapping"></a>.drop externe Tabellenzuordnung

`.drop``external` `mapping` *ExternalTableName* `json` ExternalTableName *MappingName* `table` 

Löscht die Zuordnung aus der Datenbank.
 
**Beispiel** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```

## <a name="external-sql-table"></a>Externe SQL-Tabelle

### <a name="create-or-alter-external-sql-table"></a>.create oder ändern Sie die externe SQL-Tabelle

Erstellt oder ändert eine externe SQL-Tabelle in der Datenbank, in der der Befehl ausgeführt wird.  

**Syntax**

`.create` | (`.alter` `external` ) `table` *TableName* ([columnName:columnType], ...)  
`kind` `=` `sql`  
`table``=` *SqlTableName*  
`(`*SqlServerConnectionString*`)`  
[`with` `(``docstring` `=` [ *Dokumentation*`,` `folder` `=` ] [ *Ordnername*], *property_name* `=` *Wert*`,`... `)`]


**Parameter:**

* *TableName* - Externer Tabellenname. Muss den Regeln für [Entitätsnamen](../query/schema-entities/entity-names.md)folgen. Eine externe Tabelle kann nicht denselben Namen wie eine reguläre Tabelle in derselben Datenbank haben.
* *SqlTableName* - Der Name der SQL-Tabelle.
* *SqlServerConnectionString* - Die Verbindungszeichenfolge zum SQL-Server. Dabei kann es sich um eine der folgenden Methoden handeln: 
    * **AAD-integrierte Authentifizierung** (`Authentication="Active Directory Integrated"`): Der Benutzer oder die Anwendung authentifiziert sich über AAD zu Kusto, und das gleiche Token wird dann für den Zugriff auf den SQL Server-Netzwerkendpunkt verwendet.
    * **Benutzername/Kennwortauthentifizierung** (`User ID=...; Password=...;`). Wenn die externe Tabelle für den [kontinuierlichen Export](data-export/continuous-data-export.md)verwendet wird, muss die Authentifizierung mit dieser Methode durchgeführt werden. 

> [!WARNING]
> Verbindungszeichenfolgen und Abfragen, die vertrauliche Informationen enthalten, sollten verschleiert werden, damit sie in jeder Kusto-Ablaufverfolgung weggelassen werden. Weitere Informationen finden Sie unter [verschleierte Zeichenfolgenliterale.](../query/scalar-data-types/string.md#obfuscated-string-literals)

**Optionale Eigenschaften**
| Eigenschaft            | type            | BESCHREIBUNG                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | Der Ordner der Tabelle.                  |
| `docString`         | `string`        | Eine Zeichenfolge, die die Tabelle dokumentiert.      |
| `firetriggers`      | `true`/`false`  | Wenn `true`, weist das Zielsystem an, INSERT-Trigger auszulösen, die in der SQL-Tabelle definiert sind. Der Standardwert lautet `false`. (Weitere Informationen finden Sie unter [BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx) und [System.Data.SqlClient.SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)) |
| `createifnotexists` | `true`/ `false` | Wenn `true`, wird die SQL-Zieltabelle erstellt, wenn sie noch nicht vorhanden ist. Die `primarykey` Eigenschaft muss in diesem Fall angegeben werden, um die Ergebnisspalte anzugeben, die der Primärschlüssel ist. Der Standardwert lautet `false`.  |
| `primarykey`        | `string`        | Wenn `createifnotexists` `true`dieser Wert ist, gibt der Name der Spalte im Ergebnis an, die als Primärschlüssel der SQL-Tabelle verwendet wird, wenn sie mit diesem Befehl erstellt wird.                  |

> [!NOTE]
> * Wenn die Tabelle `.create` vorhanden ist, schlägt der Befehl mit einem Fehler fehl. Verwenden `.alter` Sie diese Datei zum Ändern vorhandener Tabellen. 
> * Das Ändern des Schemas oder Formats einer externen SQL-Tabelle wird nicht unterstützt. 

Erfordert [Datenbankbenutzerberechtigungen](../management/access-control/role-based-authorization.md) für `.create` und `.alter` [Tabellenadministratorberechtigungen](../management/access-control/role-based-authorization.md) für . 
 
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
| ExternalSql | Sql       | ExternalTables | Docs      | {<br>  "TargetEntityKind": "sqltable",<br>  "TargetEntityName": "MySqlTable",<br>  "TargetEntityConnectionString": "Server=tcp:myserver.database.windows.net,1433; Authentifizierung=Active Directory Integrated;Initial Catalog=mydatabase;",<br>  "FireTriggers": wahr,<br>  "CreateIfNotExists": true,<br>  "PrimaryKey": "x"<br>} |

### <a name="querying-an-external-table-of-type-sql"></a>Abfragen einer externen Tabelle vom Typ SQL 
Ähnlich wie bei anderen Typen externer Tabellen wird das Abfragen einer externen SQL-Tabelle unterstützt. Siehe [Abfragen externer Tabellen](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data). Beachten Sie, dass die Implementierung der externen SQL-Tabellenabfrage eine vollständige "SELECT *" (oder relevante Spalten) aus der SQL-Tabelle ausführt, während der Rest der Abfrage auf der Kusto-Seite ausgeführt wird. Betrachten Sie die folgende externe Tabellenabfrage: 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto führt eine 'SELECT * from TABLE'-Abfrage in die SQL-Datenbank aus, gefolgt von einer Zählung auf Der Kusto-Seite. In solchen Fällen wird erwartet, dass die Leistung besser ist, wenn sie direkt in T-SQL ('SELECT COUNT(1) FROM TABLE') geschrieben und mit dem [sql_request-Plugin](../query/sqlrequestplugin.md)ausgeführt wird, anstatt die externe Tabellenfunktion zu verwenden. Ebenso werden Filter nicht an die SQL-Abfrage übertragen.  
Es wird empfohlen, die externe Tabelle zum Abfragen der SQL-Tabelle zu verwenden, wenn die Abfrage das Lesen der gesamten Tabelle (oder der relevanten Spalten) für die weitere Ausführung auf Kusto-Seite erfordert. Wenn eine SQL-Abfrage in T-SQL erheblich optimiert werden kann, verwenden Sie das [sql_request-Plugin](../query/sqlrequestplugin.md).
