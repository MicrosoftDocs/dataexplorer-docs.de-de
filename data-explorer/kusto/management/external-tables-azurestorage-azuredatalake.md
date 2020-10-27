---
title: Erstellen und ändern externer Tabellen in Azure Storage oder Azure Data Lake-Azure-Daten-Explorer
description: Dieser Artikel beschreibt das Erstellen und ändern externer Tabellen in Azure Storage oder Azure Data Lake
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 1d0625c949fe563084caeec936e3433c9ee70f5e
ms.sourcegitcommit: ef3d919dee27c030842abf7c45c9e82e6e8350ee
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92630108"
---
# <a name="create-and-alter-external-tables-in-azure-storage-or-azure-data-lake"></a>Erstellen und Ändern externer Tabellen in Azure Storage oder Azure Data Lake

Mit dem folgenden Befehl wird beschrieben, wie eine externe Tabelle erstellt wird, die sich in Azure BLOB Storage, Azure Data Lake Store Gen1 oder Azure Data Lake Store Gen2 befindet. 

Eine Einführung in die Funktion "externe Azure Storage Tabellen" finden [Sie Unterabfragen von Daten in Azure Data Lake mithilfe von Azure-Daten-Explorer](../../data-lake-query-data.md).

## <a name="create-or-alter-external-table"></a>. Create oder. Alter externe Tabelle

**Syntax**

( `.create`  |  `.alter`  |  `.create-or-alter` ) `external` `table` *[TableName](#table-name)* - `(` *[Schema](#schema)*`)`  
`kind` `=` (`blob` | `adl`)  
[ `partition` `by` `(` *[Partitionen](#partitions)* `)` [ `pathformat` `=` `(` *[PathFormat](#path-format)* `)` ]]  
`dataformat``=` *[Format](#format)*  
`(`*[Storageconnectionstring](#connection-string)* [ `,` ...]`)`   
[ `with` `(` *[PropertyName](#properties)* - `=` *[Wert](#properties)* `,` ... `)` ]  

Erstellt oder ändert eine neue externe Tabelle in der Datenbank, in der der Befehl ausgeführt wird.

> [!NOTE]
> * Wenn die Tabelle vorhanden ist, `.create` schlägt der Befehl mit einem Fehler fehl. Verwenden `.create-or-alter` `.alter` Sie oder, um vorhandene Tabellen zu ändern.
> * Das Ändern des Schemas, Formats oder der Partitions Definition einer externen BLOB-Tabelle wird nicht unterstützt. 
> * Der-Vorgang erfordert die [Datenbankbenutzer Berechtigung](../management/access-control/role-based-authorization.md) für `.create` und die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) für `.alter` . 

**Parameter**

<a name="table-name"></a>
*TableName*

Der Name der externen Tabelle, die den Regeln für [Entitäts Namen](../query/schema-entities/entity-names.md) entspricht.
Eine externe Tabelle kann nicht den gleichen Namen wie eine reguläre Tabelle in derselben Datenbank aufweisen.

<a name="schema"></a>
*Chaos*

Das Schema externer Daten wird im folgenden Format beschrieben:

&nbsp;&nbsp;*ColumnName* `:` *ColumnType* [ `,` *ColumnName* `:` *ColumnType* ...]

Dabei entspricht *ColumnName* den [Benennungs Regeln für Entitäten](../query/schema-entities/entity-names.md) , und *ColumnType* ist einer der [unterstützten Datentypen](../query/scalar-data-types/index.md).

> [!TIP]
> Wenn das externe Datenschema unbekannt ist, verwenden Sie das [ableiten- \_ Speicher \_ Schema](../query/inferstorageschemaplugin.md) -Plug-in, mit dem das Schema basierend auf dem Inhalt externer Dateien abgeleitet werden kann.

<a name="partitions"></a>
*Geeinigt*

Durch Trennzeichen getrennte Liste der Spalten, nach denen eine externe Tabelle partitioniert ist. Die Partitions Spalte kann in der Datendatei selbst oder im SA-Teil des Dateipfads vorhanden sein (Weitere Informationen finden Sie unter [virtuelle Spalten](#virtual-columns)).

Die Partitionsliste ist eine beliebige Kombination von Partitions Spalten, die in einem der folgenden Formate angegeben werden:

* Partition, die eine [virtuelle Spalte](#virtual-columns)darstellt.

  *PartitionName* `:` (`datetime` | `string`)

* Partition, basierend auf einem Zeichen folgen Spaltenwert.

  *PartitionName* `:` `string` `=` *ColumnName*

* Partition, basierend auf einem [Hash](../query/hashfunction.md)Wert der Zeichen folgen Spalte, Modulo *Number* .

  *PartitionName* `:` `long` `=` `hash` `(` *ColumnName* - `,` *Nummer*`)`

* Partition, basierend auf dem abgeschnittene Wert einer datetime-Spalte. Weitere Informationen finden Sie in der Dokumentation zu den Funktionen [starstarsyear](../query/startofyearfunction.md), [starstarsmonth](../query/startofmonthfunction.md), [stardesweek](../query/startofweekfunction.md), [stardesday](../query/startofdayfunction.md) oder [bin](../query/binfunction.md) .

  *PartitionName* `:` `datetime` `=` ( `startofyear` \| `startofmonth` \| `startofweek` \| `startofday` ) `(` *ColumnName*`)`  
  *PartitionName* `:` `datetime` `=` `bin` `(` *ColumnName* `,` *TimeSpan*`)`

Um die Richtigkeit der Partitionierungs Definitionen zu überprüfen, verwenden Sie die-Eigenschaft `sampleUris` beim Erstellen einer externen Tabelle.

<a name="path-format"></a>
*PathFormat*

Dateipfad-Format für externe Daten, das zusätzlich zu Partitionen angegeben werden kann. Das Pfad Format ist eine Sequenz von Partitions Elementen und Text Trennzeichen:

&nbsp;&nbsp;[ *Stringseparator* ] *Partition* [ *stringseparator* ] [ *Partition* [ *stringseparator* ]...]  

Dabei verweist *Partition* auf eine in-Klausel deklarierte Partition `partition` `by` , und *stringseparator* ist ein beliebiger Text, der in Anführungszeichen eingeschlossen ist. Aufeinander folgende Partitions Elemente müssen mithilfe von *stringseparator* getrennt festgelegt werden.

Das ursprüngliche Dateipfad-Präfix kann mithilfe von Partitions Elementen erstellt werden, die als Zeichen folgen gerendert und mit entsprechenden Text Trennzeichen Zum Angeben des Formats, das zum Rendern eines DateTime-Partitions Werts verwendet wird, kann das folgende Makro verwendet werden:

&nbsp;&nbsp;`datetime_pattern``(` *DateTimeFormat* - `,` *partitionName*`)`  

Dabei entspricht *DateTimeFormat* der .NET-Format Spezifikation mit einer Erweiterung, die das Einschließen von Format bezeichnerwerten in geschweifte Klammern zulässt. Die folgenden beiden Formate sind z. b. äquivalent:

&nbsp;&nbsp;`'year='yyyy'/month='MM` immer `year={yyyy}/month={MM}`

Standardmäßig werden DateTime-Werte in den folgenden Formaten gerendert:

| Partitionsfunktion    | Standardformat |
|-----------------------|----------------|
| `startofyear`         | `yyyy`         |
| `startofmonth`        | `yyyy/MM`      |
| `startofweek`         | `yyyy/MM/dd`   |
| `startofday`          | `yyyy/MM/dd`   |
| `bin(`*Kolumne*`, 1d)` | `yyyy/MM/dd`   |
| `bin(`*Kolumne*`, 1h)` | `yyyy/MM/dd/HH` |
| `bin(`*Kolumne*`, 1m)` | `yyyy/MM/dd/HH/mm` |

Wenn *PathFormat* in der Definition der externen Tabelle weggelassen wird, wird davon ausgegangen, dass alle Partitionen in genau derselben Reihenfolge, in der Sie definiert sind, durch Trennzeichen getrennt werden `/` . Partitionen werden mithilfe Ihrer standardmäßigen Zeichen folgen Darstellung gerendert.

Um die Richtigkeit der Pfad Format Definitionen zu überprüfen, verwenden Sie die-Eigenschaft `sampleUris` beim Erstellen einer externen Tabelle.

<a name="format"></a>
*Ges*

Das Datenformat, eines der Erfassungs [Formate](../../ingestion-supported-formats.md).

> [!NOTE]
> Die Verwendung einer externen Tabelle für das [Export Szenario](data-export/export-data-to-an-external-table.md) ist auf die folgenden Formate beschränkt: `CSV` , `TSV` `JSON` und `Parquet` .

<a name="connection-string"></a>
*StorageConnectionString*

Ein oder mehrere Pfade zu Azure BLOB Storage BLOB-Containern oder Azure Data Lake Store Dateisystemen (virtuelle Verzeichnisse oder Ordner), einschließlich der Anmelde Informationen.
Weitere Informationen finden Sie unter [Speicher Verbindungs](../api/connection-strings/storage.md) Zeichenfolgen.

> [!TIP]
> Geben Sie mehr als ein einzelnes Speicherkonto an, um eine Speicher Drosselung zu vermeiden, während Sie große Datenmengen in die externe Tabelle [exportieren](data-export/export-data-to-an-external-table.md) . Beim Export werden die Schreibvorgänge zwischen allen bereitgestellten Konten verteilt. 

<a name="properties"></a>
*Optionale Eigenschaften*

| Eigenschaft         | type     | BESCHREIBUNG       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | Tabellen Ordner                                                                     |
| `docString`      | `string` | Zeichenfolge, die die Tabelle dokumentiert                                                       |
| `compressed`     | `bool`   | Wenn festgelegt, wird angegeben, ob die Dateien als Dateien komprimiert werden `.gz` (nur im [Export Szenario](data-export/export-data-to-an-external-table.md) verwendet). |
| `includeHeaders` | `string` | Gibt bei durch Trennzeichen getrennten Textformaten (CSV, TSV,...) an, ob Dateien einen Header enthalten. Mögliche Werte: `All` (alle Dateien enthalten einen Header), `FirstFile` (die erste Datei in einem Ordner enthält einen Header), `None` (keine Dateien enthalten einen Header). |
| `namePrefix`     | `string` | Wenn festgelegt, wird das Präfix der Dateien angegeben. Bei Schreibvorgängen werden alle Dateien mit diesem Präfix geschrieben. Bei Lesevorgängen werden nur Dateien mit diesem Präfix gelesen. |
| `fileExtension`  | `string` | Wenn festgelegt, werden Dateierweiterungen der Dateien angegeben. Beim Schreiben enden Dateinamen mit diesem Suffix. Beim Lesen werden nur Dateien mit dieser Dateierweiterung gelesen.           |
| `encoding`       | `string` | Gibt an, wie der Text codiert wird: `UTF8NoBOM` (Standard) oder `UTF8BOM` .             |
| `sampleUris`     | `bool`   | Wenn festgelegt, liefert das Befehls Ergebnis mehrere Beispiele für den URI externer Datendateien, da Sie von der Definition der externen Tabelle erwartet werden (die Beispiele werden in der zweiten Ergebnistabelle zurückgegeben). Mit dieser Option können Sie überprüfen, ob *[Partitionen](#partitions)* und *[PathFormat](#path-format)* -Parameter ordnungsgemäß definiert sind. |
| `validateNotEmpty` | `bool`   | Wenn diese Einstellung festgelegt ist, werden die Verbindungs Zeichenfolgen überprüft, damit Sie Inhalte aufweisen. Der Befehl schlägt fehl, wenn der angegebene URI-Speicherort nicht vorhanden ist, oder wenn keine ausreichenden Zugriffsberechtigungen vorhanden sind. |

> [!TIP]
> Weitere Informationen zu den Rollen `namePrefix` und `fileExtension` Eigenschaften, die beim Filtern von Datendateien während der Abfrage abgespielt werden, finden Sie im Abschnitt [Dateifilter Logik](#file-filtering) .
 
<a name="examples"></a>
**Beispiele** 

Eine nicht partitionierte externe Tabelle. Es wird erwartet, dass Datendateien direkt unter dem definierten Container platziert werden:

```kusto
.create external table ExternalTable (x:long, s:string)  
kind=blob 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

Eine externe Tabelle, die nach Datum partitioniert ist. Es wird erwartet, dass Datums Dateien in Verzeichnissen des DateTime-Standard Formats platziert werden `yyyy/MM/dd` :

```kusto
.create external table ExternalTable (Timestamp:datetime, x:long, s:string) 
kind=adl
partition by (Date:datetime = bin(Timestamp, 1d)) 
dataformat=csv 
( 
   h@'abfss://filesystem@storageaccount.dfs.core.windows.net/path;secretKey'
)
```

Eine externe Tabelle, die nach Monat mit dem Verzeichnis Format partitioniert wird `year=yyyy/month=MM` :

```kusto
.create external table ExternalTable (Timestamp:datetime, x:long, s:string) 
kind=blob 
partition by (Month:datetime = startofmonth(Timestamp)) 
pathformat = (datetime_pattern("'year='yyyy'/month='MM", Month)) 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

Eine externe Tabelle, die zuerst nach Kunden Name und dann nach Datum partitioniert wird. Die erwartete Verzeichnisstruktur ist z. b. `customer_name=Softworks/2019/02/01` :

```kusto
.create external table ExternalTable (Timestamp:datetime, CustomerName:string) 
kind=blob 
partition by (CustomerNamePart:string = CustomerName, Date:datetime = startofday(Timestamp)) 
pathformat = ("customer_name=" CustomerNamePart "/" Date)
dataformat=csv 
(  
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
)
```

Eine externe Tabelle, die zuerst nach dem Kundennamen Hash (Modulo 10) und dann nach dem Datum partitioniert wird. Die erwartete Verzeichnisstruktur ist z. b `customer_id=5/dt=20190201` .. Die Namen der Datendateien enden mit der `.txt` Erweiterung:

```kusto
.create external table ExternalTable (Timestamp:datetime, CustomerName:string) 
kind=blob 
partition by (CustomerId:long = hash(CustomerName, 10), Date:datetime = startofday(Timestamp)) 
pathformat = ("customer_id=" CustomerId "/dt=" datetime_pattern("yyyyMMdd", Date)) 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with (fileExtension = ".txt")
```

**Beispielausgabe**

|TableName|TableType|Ordner|DocString|Eigenschaften|ConnectionStrings|Partitionen|PathFormat|
|---------|---------|------|---------|----------|-----------------|----------|----------|
|ExternalTable|Blob|Externaltables|Docs|{"Format": "CSV", "Compressed": false, "compressionType": NULL, "File Extension": NULL, "includeheaders": "None", "Encoding": NULL, "NamePrefix": NULL}|["https://storageaccount.blob.core.windows.net/container1;\*\*\*\*\*\*\*"]|[{"Mod": 10, "Name": "CustomerID", "ColumnName": "CustomerName", "Ordinal": 0}, {"Function": "stardebday", "Name": "Date", "ColumnName": "timestamp", "Ordinal": 1}]|"Customer \_ ID =" CustomerID "/DT =" DateTime \_ Pattern ("YYYYMMDD", Date)|

<a name="virtual-columns"></a>
**Virtuelle Spalten**

Beim Exportieren von Daten aus Spark werden Partitions Spalten (die in der-Methode des dataframe-Writers angegeben sind `partitionBy` ) nicht in Datendateien geschrieben. Durch diesen Prozess wird die Daten Duplizierung vermieden, da die Daten bereits in "Ordner" Namen vorhanden sind. Beispielsweise `column1=<value>/column2=<value>/` können von und Spark beim Lesen erkannt werden.

Externe Tabellen unterstützen die folgende Syntax zum angeben virtueller Spalten:

```kusto
.create external table ExternalTable (EventName:string, Revenue:double)  
kind=blob  
partition by (CustomerName:string, Date:datetime)  
pathformat = ("customer=" CustomerName "/date=" datetime_pattern("yyyyMMdd", Date))  
dataformat=parquet
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

<a name="file-filtering"></a>
**Logik zum Filtern von Dateien**

Beim Abfragen einer externen Tabelle verbessert die Abfrage-Engine die Leistung, indem irrelevante externe Speicherdateien herausgefiltert werden. Der Vorgang zum Durchlaufen von Dateien und zum Festlegen, ob eine Datei verarbeitet werden soll, wird unten beschrieben.

1. Erstellen Sie ein URI-Muster, das eine Stelle darstellt, an der Dateien gefunden werden. Anfänglich ist das URI-Muster eine Verbindungs Zeichenfolge, die als Teil der externen Tabellendefinition bereitgestellt wird. Wenn Partitionen definiert sind, werden Sie mit *[PathFormat](#path-format)* gerendert und dann an das URI-Muster angehängt.

2. Überprüfen Sie für alle Dateien, die unter den erstellten URI-Mustern gefunden werden, Folgendes:

   * Partitionswerte entsprechen Prädikaten, die in einer Abfrage verwendet werden.
   * Der BLOB-Name beginnt mit `NamePrefix` , wenn eine solche Eigenschaft definiert ist.
   * Der BLOB-Name endet mit `FileExtension` , wenn eine solche Eigenschaft definiert ist.

Sobald alle Bedingungen erfüllt sind, wird die Datei von der Abfrage-Engine abgerufen und verarbeitet.

> [!NOTE]
> Das anfängliche URI-Muster wird mithilfe von Abfrage Prädikat Werten erstellt. Dies funktioniert am besten für einen begrenzten Satz von Zeichen folgen Werten sowie für geschlossene Zeiträume.

## <a name="show-external-table-artifacts"></a>. anzeigen externer Tabellen Artefakte

Gibt eine Liste aller Dateien zurück, die verarbeitet werden, wenn eine bestimmte externe Tabelle abgefragt wird.

> [!NOTE]
> Der Vorgang erfordert die Berechtigung für die [Datenbankbenutzer](../management/access-control/role-based-authorization.md).

**Syntax:** 

`.show``external` `table` *TableName* `artifacts` [ `limit` *maxResults* ]

Dabei ist " *maxResults* " ein optionaler Parameter, der so festgelegt werden kann, dass die Anzahl der Ergebnisse beschränkt wird.

**Ausgabe**

| Ausgabeparameter | type   | Beschreibung                       |
|------------------|--------|-----------------------------------|
| URI              | Zeichenfolge | URI der externen Speicher Datendatei |
| Size             | long   | Dateilänge in Byte              |
| Partition        | dynamisch | Dynamisches Objekt, das Datei Partitionen für eine partitionierte externe Tabelle beschreibt |

> [!TIP]
> Das Durchlaufen aller Dateien, auf die von einer externen Tabelle verwiesen wird, kann in Abhängigkeit von der Anzahl der Dateien recht kostspielig sein. Stellen Sie sicher, dass Sie den Parameter verwenden, `limit` Wenn Sie nur einige URI-Beispiele sehen möchten.

**Beispiele:**

```kusto
.show external table T artifacts
```

**Ausgabe:**

| Uri                                                                     | Size | Partition |
|-------------------------------------------------------------------------| ---- | --------- |
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` | 10743 | `{}`   |


Für eine partitionierte Tabelle `Partition` enthält die Spalte extrahierte Partitionswerte:

**Ausgabe:**

| Uri                                                                     | Size | Partition |
|-------------------------------------------------------------------------| ---- | --------- |
| `https://storageaccount.blob.core.windows.net/container1/customer=john.doe/dt=20200101/file.csv` | 10743 | `{"Customer": "john.doe", "Date": "2020-01-01T00:00:00.0000000Z"}` |


## <a name="create-external-table-mapping"></a>. Erstellen einer externen Tabellen Zuordnung

`.create``external` `table` *Externaltablename* `json` `mapping` *MappingName* *mappinginjsonformat*

Erstellt eine neue Zuordnung. Weitere Informationen finden Sie unter [Daten](./mappings.md#json-mapping)Zuordnungen.

**Beispiel** 
 
```kusto
.create external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**Beispielausgabe**

| Name     | Art | Zuordnung                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "Properties": {"Path": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "Properties": {"Path": "$. ROWGUID"}}] |

## <a name="alter-external-table-mapping"></a>. Alter externer Tabellen Zuordnung

`.alter``external` `table` *Externaltablename* `json` `mapping` *MappingName* *mappinginjsonformat*

Ändert eine vorhandene Zuordnung. 
 
**Beispiel** 
 
```kusto
.alter external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**Beispielausgabe**

| Name     | Art | Zuordnung                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "Properties": {"Path": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "Properties": {"Path": "$. ROWGUID"}}] |

## <a name="show-external-table-mappings"></a>. Zuordnungen externer Tabellen anzeigen

`.show``external` `table` *Externaltablename* `json` `mapping` *MappingName* 

`.show``external` `table` *Externaltablename* `json``mappings`

Zeigt die Zuordnungen an (alle oder die durch den Namen angegebenen).
 
**Beispiel** 
 
```kusto
.show external table MyExternalTable json mapping "Mapping1" 

.show external table MyExternalTable json mappings 
```

**Beispielausgabe**

| Name     | Art | Zuordnung                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "Properties": {"Path": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "Properties": {"Path": "$. ROWGUID"}}] |

## <a name="drop-external-table-mapping"></a>. Löschen der Zuordnung externer Tabellen

`.drop``external` `table` *Externaltablename* `json` `mapping` *MappingName* 

Löscht die Zuordnung aus der Datenbank.
 
**Beispiel** 
 
```kusto
.drop external table MyExternalTable json mapping "Mapping1" 
```
## <a name="next-steps"></a>Nächste Schritte

* [Externe Tabellen Abfragen](../../data-lake-query-data.md).
* [Exportieren von Daten in eine externe Tabelle](data-export/export-data-to-an-external-table.md).
* [Fortlaufender Datenexport in eine externe Tabelle](data-export/continuous-data-export.md).
