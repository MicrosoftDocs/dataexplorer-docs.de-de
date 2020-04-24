---
title: Datenzuordnungen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Datenzuordnungen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 0d94815eedfd551a09a979c57c68baf125abec40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520773"
---
# <a name="data-mappings"></a>Datenzuordnungen

Datenzuordnungen werden während der Aufnahme verwendet, um eingehende Daten Spalten in Kusto-Tabellen zuzuordnen.

Kusto unterstützt verschiedene Arten von `row-oriented` Zuordnungen, sowohl (CSV, `column-oriented` JSON und AVRO) als auch (Parquet).

Jedes Element in der Zuordnungsliste besteht aus drei Eigenschaften:

|Eigenschaft|Beschreibung|
|----|--|
|`column`|Zielspaltenname in der Kusto-Tabelle|
|`datatype`| (Optional) Datentyp, mit dem die zugeordnete Spalte erstellt werden soll, wenn sie noch nicht in der Kusto-Tabelle vorhanden ist|
|`Properties`|(Optional) Property-Bag, der für jede Zuordnung spezifische Eigenschaften enthält, wie in jedem Abschnitt unten beschrieben.


Alle Zuordnungen können [vorerstellt](create-ingestion-mapping-command.md) werden und können über den `ingestionMappingReference` Befehl ingest mithilfe von Parametern referenziert werden.

## <a name="csv-mapping"></a>CSV-Zuordnung

Wenn es sich bei der Quelldatei um ein CSV-Format (oder ein beliebiges delimeter-getrenntes Format) handelt und ihr Schema nicht mit dem aktuellen Kusto-Tabellenschema übereinstimmt, wird eine CSV-Zuordnung aus dem Dateischema dem Kusto-Tabellenschema zugeordnet. Wenn die Tabelle in Kusto nicht vorhanden ist, wird sie gemäß dieser Zuordnung erstellt. Wenn einige Felder in der Zuordnung in der Tabelle fehlen, werden sie hinzugefügt. 

Die CSV-Zuordnung kann auf alle Trennzeichen-getrennten Formate angewendet werden: CSV, TSV, PSV, SCSV und SOHsv.

Jedes Element in der Liste beschreibt eine Zuordnung für eine bestimmte Spalte und kann die folgenden Eigenschaften enthalten:

|Eigenschaft|Beschreibung|
|----|--|
|`ordinal`|Die Spaltenauftragsnummer in CSV|
|`constantValue`|(Optional) Der konstante Wert, der für eine Spalte anstelle eines Werts innerhalb des CSV verwendet werden soll|

> [!NOTE]
> `Ordinal`und `ConstantValue` schließen sich gegenseitig aus.

### <a name="example-of-the-csv-mapping"></a>Beispiel für die CSV-Zuordnung

``` json
[
  { "column" : "rownumber", "Properties":{"Ordinal":"0"}},
  { "column" : "rowguid",   "Properties":{"Ordinal":"1"}},
  { "column" : "xdouble",   "Properties":{"Ordinal":"2"}},
  { "column" : "xbool",     "Properties":{"Ordinal":"3"}},
  { "column" : "xint32",    "Properties":{"Ordinal":"4"}},
  { "column" : "xint64",    "Properties":{"Ordinal":"5"}},
  { "column" : "xdate",     "Properties":{"Ordinal":"6"}},
  { "column" : "xtext",     "Properties":{"Ordinal":"7"}},
  { "column" : "const_val", "Properties":{"ConstValue":"Sample: constant value"}}
]
```

> [!NOTE]
> Wenn die obige Zuordnung als `.ingest` Teil des Steuerelementbefehls bereitgestellt wird, wird sie als JSON-Zeichenfolge serialisiert.

* Wenn die obige Zuordnung [vorerstellt wird,](create-ingestion-mapping-command.md) `.ingest` kann im Steuerbefehl darauf verwiesen werden:
```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

* Wenn die obige Zuordnung als `.ingest` Teil des Steuerelementbefehls bereitgestellt wird, wird sie als JSON-Zeichenfolge serialisiert:

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMapping = 
        "["
            "{\"column\":\"rownumber\",\"Properties\":{\"Ordinal\":0}},"
            "{\"column\":\"rowguid\",  \"Properties\":{\"Ordinal\":1}}"
        "]" 
    )
```

**Hinweis:** Das folgende Zuordnungsformat `Properties` ohne den Property-Bag wird derzeit unterstützt, kann aber in Zukunft veraltet sein.

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMapping = 
        "["
            "{\"column\":\"rownumber\",\"Ordinal\": 0},"
            "{\"column\":\"rowguid\",  \"Ordinal\": 1}"
        "]" 
    )
```

## <a name="json-mapping"></a>JSON-Zuordnung

Wenn die Quelldatei im JSON-Format ist, wird der Dateiinhalt der Kusto-Tabelle zugeordnet. Die Tabelle muss in der Kusto-Datenbank vorhanden sein, es sei denn, für alle zugeordneten Spalten wird ein gültiger Datentyp angegeben. Die in der JSON-Zuordnung zugeordneten Spalten müssen in der Kusto-Tabelle vorhanden sein, es sei denn, für alle nicht vorhandenen Spalten wird ein Datentyp angegeben.

Jedes Element in der Liste beschreibt eine Zuordnung für eine bestimmte Spalte und kann die folgenden Eigenschaften enthalten: 

|Eigenschaft|Beschreibung|
|----|--|
|`path`|Wenn beginnt `$`mit : JSON-Pfad zu dem Feld, das zum Inhalt der Spalte im JSON-Dokument wird (JSON-Pfad, der das gesamte Dokument bezeichnet ist `$`). Wenn der Wert nicht `$`mit beginnt: ein konstanter Wert wird verwendet.|
|`transform`|(Optional) Transformation, die auf den Inhalt mit [Zuordnungstransformationen](#mapping-transformations)angewendet werden soll.|

### <a name="example-of-json-mapping"></a>Beispiel für JSON-Zuordnung

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "rowguid",     "Properties":{"Path":"$.rowguid"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint32",      "Properties":{"Path":"$.xint32"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```

> [!NOTE]
> Wenn die obige Zuordnung als `.ingest` Teil des Steuerelementbefehls bereitgestellt wird, wird sie als JSON-Zeichenfolge serialisiert.

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

**Hinweis:** Das folgende Zuordnungsformat `Properties` ohne den Property-Bag wird derzeit unterstützt, kann aber in Zukunft veraltet sein.

```
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "json", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"path\":\"$.rownumber\"},"
        "{\"column\":\"rowguid\",  \"path\":\"$.rowguid\"}"
      "]"
  )
```
    
## <a name="avro-mapping"></a>Avro-Mapping

Wenn die Quelldatei im Avro-Format ist, wird der Avro-Dateiinhalt der Kusto-Tabelle zugeordnet. Die Tabelle muss in der Kusto-Datenbank vorhanden sein, es sei denn, für alle zugeordneten Spalten wird ein gültiger Datentyp angegeben. Die in der Avro-Zuordnung zugeordneten Spalten müssen in der Kusto-Tabelle vorhanden sein, es sei denn, für alle nicht vorhandenen Spalten wird ein Datentyp angegeben.

Jedes Element in der Liste beschreibt eine Zuordnung für eine bestimmte Spalte und kann die folgenden Eigenschaften enthalten: 

|Eigenschaft|Beschreibung|
|----|--|
|`Field`|Der Name des Felds im Avro-Datensatz.|
|`Path`|Alternative zur `field` Verwendung, die es ermöglicht, den inneren Teil eines Avro-Datensatzfeldes zu nehmen, falls erforderlich. Der Wert bezeichnet einen JSON-Pfad aus dem Stamm des Datensatzes. Weitere Informationen finden Sie in den folgenden Hinweisen. |
|`transform`|(Optional) Transformation, die auf den Inhalt mit [unterstützten Transformationen](#mapping-transformations)angewendet werden soll.|

**Hinweise**
>[!NOTE]
> * `field`und `path` kann nicht zusammen verwendet werden, nur eine ist erlaubt. 
> * `path`kann nicht `$` nur auf Root zeigen, es muss mindestens eine Pfadebene haben.

Die beiden folgenden Alternativen sind gleich:

``` json
[
  {"column": "rownumber", "Properties":{"Path":"$.RowNumber"}}
]
```

``` json
[
  {"column": "rownumber", "Properties":{"Field":"RowNumber"}}
]
```

### <a name="example-of-the-avro-mapping"></a>Beispiel für die AVRO-Zuordnung

``` json
[
  {"column": "rownumber", "Properties":{"Field":"rownumber"}},
  {"column": "rowguid",   "Properties":{"Field":"rowguid"}},
  {"column": "xdouble",   "Properties":{"Field":"xdouble"}},
  {"column": "xboolean",  "Properties":{"Field":"xboolean"}},
  {"column": "xint32",    "Properties":{"Field":"xint32"}},
  {"column": "xint64",    "Properties":{"Field":"xint64"}},
  {"column": "xdate",     "Properties":{"Field":"xdate"}},
  {"column": "xtext",     "Properties":{"Field":"xtext"}}
]
``` 

> [!NOTE]
> Wenn die obige Zuordnung als `.ingest` Teil des Steuerelementbefehls bereitgestellt wird, wird sie als JSON-Zeichenfolge serialisiert.

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

**Hinweis:** Das folgende Zuordnungsformat `Properties` ohne den Property-Bag wird derzeit unterstützt, kann aber in Zukunft veraltet sein.

```
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "avro", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"field\":\"rownumber\"},"
        "{\"column\":\"rowguid\",  \"field\":\"rowguid\"}"
      "]"
  )
```

## <a name="parquet-mapping"></a>Parkettkartierung

Wenn die Quelldatei im Parkettformat ist, wird der Dateiinhalt der Kusto-Tabelle zugeordnet. Die Tabelle muss in der Kusto-Datenbank vorhanden sein, es sei denn, für alle zugeordneten Spalten wird ein gültiger Datentyp angegeben. Die in der Parkettzuordnung zugeordneten Spalten müssen in der Kusto-Tabelle vorhanden sein, es sei denn, für alle nicht vorhandenen Spalten wird ein Datentyp angegeben.

Jedes Element in der Liste beschreibt eine Zuordnung für eine bestimmte Spalte und kann die folgenden Eigenschaften enthalten:

|Eigenschaft|Beschreibung|
|----|--|
|`path`|Wenn beginnt `$`mit : JSON-Pfad zu dem Feld, das zum Inhalt der Spalte im Parkettdokument wird (JSON-Pfad, der das gesamte Dokument bezeichnet ist `$`). Wenn der Wert nicht `$`mit beginnt: ein konstanter Wert wird verwendet.|
|`transform`|(Optional) [Zuordnungvontransformationen,](#mapping-transformations) die auf den Inhalt angewendet werden sollen.


### <a name="example-of-the-parquet-mapping"></a>Beispiel für das Parkett-Mapping

``` json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```      

> [!NOTE]
> Wenn die obige Zuordnung als `.ingest` Teil des Steuerelementbefehls bereitgestellt wird, wird sie als JSON-Zeichenfolge serialisiert.

* Wenn die obige Zuordnung [vorerstellt wird,](create-ingestion-mapping-command.md) `.ingest` kann im Steuerbefehl darauf verwiesen werden:

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

* Wenn die obige Zuordnung als `.ingest` Teil des Steuerelementbefehls bereitgestellt wird, wird sie als JSON-Zeichenfolge serialisiert:

```
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "parquet", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

## <a name="orc-mapping"></a>Ork-Mapping

Wenn die Quelldatei im Ork-Format ist, wird der Dateiinhalt der Kusto-Tabelle zugeordnet. Die Tabelle muss in der Kusto-Datenbank vorhanden sein, es sei denn, für alle zugeordneten Spalten wird ein gültiger Datentyp angegeben. Die in der Orc-Zuordnung zugeordneten Spalten müssen in der Kusto-Tabelle vorhanden sein, es sei denn, für alle nicht vorhandenen Spalten wird ein Datentyp angegeben.

Jedes Element in der Liste beschreibt eine Zuordnung für eine bestimmte Spalte und kann die folgenden Eigenschaften enthalten:

|Eigenschaft|Beschreibung|
|----|--|
|`path`|Wenn beginnt `$`mit : JSON-Pfad zu dem Feld, das zum Inhalt der Spalte im Orc-Dokument wird (JSON-Pfad, der das gesamte Dokument bezeichnet ist `$`). Wenn der Wert nicht `$`mit beginnt: ein konstanter Wert wird verwendet.|
|`transform`|(Optional) [Zuordnungvontransformationen,](#mapping-transformations) die auf den Inhalt angewendet werden sollen.

### <a name="example-of-orc-mapping"></a>Beispiel für Ork-Mapping

``` json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```      

> [!NOTE]
> Wenn die obige Zuordnung als `.ingest` Teil des Steuerelementbefehls bereitgestellt wird, wird sie als JSON-Zeichenfolge serialisiert.

```
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "orc", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

## <a name="mapping-transformations"></a>Mapping-Transformationen

Einige der Datenformatzuordnungen (Parquet, JSON und Avro) unterstützen einfache und nützliche Ingest-Time-Transformationen. Wenn das Szenario eine komplexere Verarbeitung zu aufnahmeweise erfordert, verwenden Sie [die Update-Richtlinie](update-policy.md), die es ermöglicht, eine einfache Verarbeitung mithilfe des KQL-Ausdrucks zu definieren.

|Pfadabhängige Transformation|Beschreibung|Bedingungen|
|--|--|--|
|`PropertyBagArrayToDictionary`|Transformiert das JSON-Array von Eigenschaften (z. B. "events:["n1":"v1", "n2":"v2"]" in wörterbuchund serialisiert es in ein gültiges JSON-Dokument (z. B. "n1":"v1","n2":"v2".|Kann nur angewendet `path` werden, wenn verwendet wird|
|`GetPathElement(index)`|Extrahiert ein Element aus dem angegebenen Pfad entsprechend dem angegebenen Index (z. B. Pfad: .a.b.c, GetPathElement(0) == "c", GetPathElement(-1) == "b", Typstring|Kann nur angewendet `path` werden, wenn verwendet wird|
|`SourceLocation`|Name des Speicherartefakts, das die Daten bereitgestellt hat, Typzeichenfolge (z. B. das Feld "BaseUri" des Blobs).|
|`SourceLineNumber`|Offset relativ zu diesem Speicherartefakt, geben Sie long ein (beginnend mit '1' und Inkrementierung pro neuem Datensatz).|
|`DateTimeFromUnixSeconds`|Konvertiert die Zahl, die unix-zeit (Sekunden seit 1970-01-01) in UTC datetime string darstellt|
|`DateTimeFromUnixMilliseconds`|Konvertiert die Zahl, die unix-zeit (Millisekunden seit 1970-01-01) in UTC datetime string darstellt|
|`DateTimeFromUnixMicroseconds`|Konvertiert die Zahl, die unix-zeit (Mikrosekunden seit 1970-01-01) in UTC datetime string darstellt|
|`DateTimeFromUnixNanoseconds`|Konvertiert die Zahl, die unix-zeit (Nanosekunden seit 1970-01-01) in UTC datetime string darstellt|