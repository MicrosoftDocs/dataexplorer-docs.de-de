---
title: 'Daten Zuordnungen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden Daten Zuordnungen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: ohbitton
ms.service: data-explorer
ms.topic: reference
ms.date: 05/19/2020
ms.openlocfilehash: cd498d43d98250bad0a7ce00c4a8fec7b4f3ad4f
ms.sourcegitcommit: d08b3344d7e9a6201cf01afc8455c7aea90335aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964726"
---
# <a name="data-mappings"></a>Datenzuordnungen

Daten Zuordnungen werden während der Erfassung verwendet, um eingehende Datenspalten in Kusto-Tabellen zuzuordnen.

Kusto unterstützt verschiedene Typen von Zuordnungen, sowohl `row-oriented` (CSV, JSON und Avro) als auch `column-oriented` (Parkett).

Jedes Element in der Zuordnung Liste wird aus drei Eigenschaften erstellt:

|Eigenschaft|BESCHREIBUNG|
|----|--|
|`column`|Name der Ziel Spalte in der Kusto-Tabelle|
|`datatype`| Optionale Der Datentyp, mit dem die zugeordnete Spalte erstellt werden soll, wenn Sie in der Kusto-Tabelle nicht bereits vorhanden ist.|
|`Properties`|Optionale Eigenschaften Auflistung, die Eigenschaften enthält, die für jede Zuordnung spezifisch sind, wie in den folgenden Abschnitten beschrieben.


Alle Zuordnungen können [vorab erstellt](create-ingestion-mapping-command.md) werden, und auf Sie kann über den Erfassungs Befehl mithilfe von `ingestionMappingReference` Parametern verwiesen werden.

## <a name="csv-mapping"></a>CSV-Zuordnung

Wenn die Quelldatei eine CSV-Datei (oder ein durch Trennzeichen getrenntes Format) ist und das zugehörige Schema nicht mit dem aktuellen Kusto-Tabellen Schema identisch ist, wird eine CSV-Zuordnung vom Datei Schema zum Kusto-Tabellen Schema zugeordnet. Wenn die Tabelle nicht in Kusto vorhanden ist, wird Sie gemäß dieser Zuordnung erstellt. Wenn einige Felder in der Zuordnung in der Tabelle fehlen, werden Sie hinzugefügt. 

CSV-Zuordnung kann auf alle Formate angewendet werden, die durch Trennzeichen getrennt sind: CSV, TSV, PSV, scsv und sohsv.

Jedes Element in der Liste beschreibt eine Zuordnung für eine bestimmte Spalte und kann die folgenden Eigenschaften enthalten:

|Eigenschaft|BESCHREIBUNG|
|----|--|
|`ordinal`|Die Spaltenreihenfolge-Nummer in CSV|
|`constantValue`|Optionale Der Konstante Wert, der für eine Spalte anstelle eines Werts im CSV verwendet werden soll.|

> [!NOTE]
> `Ordinal` und `ConstantValue` schließen sich gegenseitig aus.

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
> Wenn die oben genannte Zuordnung als Teil des Control-Befehls bereitgestellt wird, `.ingest` wird Sie als JSON-Zeichenfolge serialisiert.

* Wenn die zuvor erstellte Zuordnung [vorab erstellt wurde](create-ingestion-mapping-command.md) , kann im Befehl Control auf Sie verwiesen werden `.ingest` :

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

* Wenn die oben genannte Zuordnung als Teil des Control-Befehls bereitgestellt wird, `.ingest` wird Sie als JSON-Zeichenfolge serialisiert:

```kusto
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

**Hinweis:** Das folgende Mapping-Format, ohne den `Properties` Eigenschaften Behälter, ist veraltet.

```kusto
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

Wenn die Quelldatei im JSON-Format vorliegt, wird der Dateiinhalt der Kusto-Tabelle zugeordnet. Die Tabelle muss in der Kusto-Datenbank vorhanden sein, es sei denn, für alle zugeordneten Spalten ist ein gültiger Datentyp angegeben. Die Spalten, die in der JSON-Zuordnung zugeordnet sind, müssen in der Kusto-Tabelle vorhanden sein, es sei denn, für alle nicht vorhandenen Spalten ist ein Datentyp angegeben.

Jedes Element in der Liste beschreibt eine Zuordnung für eine bestimmte Spalte und kann die folgenden Eigenschaften enthalten: 

|Eigenschaft|BESCHREIBUNG|
|----|--|
|`path`|Wenn mit `$` : JSON-Pfad zu dem Feld beginnt, das zum Inhalt der Spalte im JSON-Dokument wird (JSON-Pfad, der das gesamte Dokument bezeichnet `$` ). Wenn der Wert nicht mit beginnt `$` : ein konstanter Wert wird verwendet.|
|`transform`|Optionale Transformation, die auf den Inhalt mit Mapping- [Transformationen](#mapping-transformations)angewendet werden soll.|

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
> Wenn die oben genannte Zuordnung als Teil des Control-Befehls bereitgestellt wird, `.ingest` wird Sie als JSON-Zeichenfolge serialisiert.

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

**Hinweis:** Das folgende Mapping-Format, ohne den `Properties` Eigenschaften Behälter, ist veraltet.

```kusto
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
    
## <a name="avro-mapping"></a>Avro-Zuordnung

Wenn die Quelldatei im Avro-Format vorliegt, wird der Inhalt von Avro-Dateien der Kusto-Tabelle zugeordnet. Die Tabelle muss in der Kusto-Datenbank vorhanden sein, es sei denn, für alle zugeordneten Spalten ist ein gültiger Datentyp angegeben. Die Spalten, die in der Avro-Zuordnung zugeordnet sind, müssen in der Kusto-Tabelle vorhanden sein, es sei denn, für alle nicht vorhandenen Spalten ist ein Datentyp angegeben.

Jedes Element in der Liste beschreibt eine Zuordnung für eine bestimmte Spalte und kann die folgenden Eigenschaften enthalten: 

|Eigenschaft|BESCHREIBUNG|
|----|--|
|`Field`|Der Name des Felds im Avro-Datensatz.|
|`Path`|Eine Alternative zur Verwendung `field` von, die die Verwendung des inneren Teils eines Avro-Daten Satz Felds ermöglicht, falls erforderlich. Der Wert bezeichnet einen JSON-Pfad vom Stamm des Datensatzes. Weitere Informationen finden Sie in den Hinweisen unten. |
|`transform`|Optionale Transformation, die auf den Inhalt mit [unterstützten Transformationen](#mapping-transformations)angewendet werden soll.|

**Notizen**
>[!NOTE]
> * `field` und `path` können nicht gleichzeitig verwendet werden, es ist nur eine zulässig. 
> * `path` kann nicht nur auf root verweisen `$` , sondern muss mindestens eine Pfad Ebene aufweisen.

Die folgenden beiden Alternativen sind gleich:

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

### <a name="example-of-the-avro-mapping"></a>Beispiel für die Avro-Zuordnung

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
> Wenn die oben genannte Zuordnung als Teil des Control-Befehls bereitgestellt wird, `.ingest` wird Sie als JSON-Zeichenfolge serialisiert.

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

**Hinweis:** Das folgende Mapping-Format, ohne den `Properties` Eigenschaften Behälter, ist veraltet.

```kusto
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

## <a name="parquet-mapping"></a>Parkett Zuordnung

Wenn die Quelldatei im Parkett Format vorliegt, wird der Inhalt der Datei der Kusto-Tabelle zugeordnet. Die Tabelle muss in der Kusto-Datenbank vorhanden sein, es sei denn, für alle zugeordneten Spalten ist ein gültiger Datentyp angegeben. Die in der Parkett Zuordnung zugeordneten Spalten müssen in der Kusto-Tabelle vorhanden sein, es sei denn, für alle nicht vorhandenen Spalten ist ein Datentyp angegeben.

Jedes Element in der Liste beschreibt eine Zuordnung für eine bestimmte Spalte und kann die folgenden Eigenschaften enthalten:

|Eigenschaft|BESCHREIBUNG|
|----|--|
|`path`|Wenn beginnt mit `$` : JSON-Pfad zum Feld, das zum Inhalt der Spalte im Parkett Dokument wird (JSON-Pfad, der das gesamte Dokument bezeichnet `$` ). Wenn der Wert nicht mit beginnt `$` : ein konstanter Wert wird verwendet.|
|`transform`|Optionale [Mapping von Transformationen](#mapping-transformations) , die auf den Inhalt angewendet werden sollen.


### <a name="example-of-the-parquet-mapping"></a>Beispiel für die Parkett Zuordnung

```json
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
> Wenn die oben genannte Zuordnung als Teil des Control-Befehls bereitgestellt wird, `.ingest` wird Sie als JSON-Zeichenfolge serialisiert.

* Wenn die zuvor erstellte Zuordnung [vorab erstellt wurde](create-ingestion-mapping-command.md) , kann im Befehl Control auf Sie verwiesen werden `.ingest` :

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

* Wenn die oben genannte Zuordnung als Teil des Control-Befehls bereitgestellt wird, `.ingest` wird Sie als JSON-Zeichenfolge serialisiert:

```kusto
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

## <a name="orc-mapping"></a>Orc-Zuordnung

Wenn die Quelldatei im Orc-Format vorliegt, wird der Dateiinhalt der Kusto-Tabelle zugeordnet. Die Tabelle muss in der Kusto-Datenbank vorhanden sein, es sei denn, für alle zugeordneten Spalten ist ein gültiger Datentyp angegeben. Die Spalten, die in der Orc-Zuordnung zugeordnet sind, müssen in der Kusto-Tabelle vorhanden sein, es sei denn, für alle nicht vorhandenen Spalten ist ein Datentyp angegeben.

Jedes Element in der Liste beschreibt eine Zuordnung für eine bestimmte Spalte und kann die folgenden Eigenschaften enthalten:

|Eigenschaft|BESCHREIBUNG|
|----|--|
|`path`|Wenn beginnt mit `$` : JSON-Pfad zum Feld, das zum Inhalt der Spalte im Orc-Dokument wird (JSON-Pfad, der das gesamte Dokument bezeichnet `$` ). Wenn der Wert nicht mit beginnt `$` : ein konstanter Wert wird verwendet.|
|`transform`|Optionale [Mapping von Transformationen](#mapping-transformations) , die auf den Inhalt angewendet werden sollen.

### <a name="example-of-orc-mapping"></a>Beispiel für Orc-Zuordnung

```json
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
> Wenn die oben genannte Zuordnung als Teil des Control-Befehls bereitgestellt wird, `.ingest` wird Sie als JSON-Zeichenfolge serialisiert.

```kusto
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

## <a name="mapping-transformations"></a>Mapping von Transformationen

Einige der Datenformat Zuordnungen (Parkett, JSON und Avro) unterstützen einfache und nützliche Erfassungs Zeit Transformationen. Wenn für das Szenario eine komplexere Verarbeitung zur Erfassungs Zeit erforderlich ist, verwenden Sie die [Update Richtlinie](update-policy.md), die das Definieren der Lightweight-Verarbeitung mithilfe des kql-Ausdrucks ermöglicht.

|Pfad abhängige Transformation|BESCHREIBUNG|Bedingungen|
|--|--|--|
|`PropertyBagArrayToDictionary`|Wandelt das JSON-Array von Eigenschaften (z. b. {Events: [{"N1": "V1"}, {"N2": "V2"}]}) in das Wörterbuch um und serialisiert es in ein gültiges JSON-Dokument (z. b. {"N1": "V1", "N2": "V2"}).|Kann nur angewendet werden, wenn `path` verwendet wird.|
|`SourceLocation`|Der Name des Speicher Artefakts, von dem die Daten bereitgestellt wurden, geben Sie Zeichenfolge ein (z. b. das Feld "baseUri" des BLOBs).|
|`SourceLineNumber`|Offset relativ zu diesem Speicher Element, Typ Long (beginnend mit "1" und Inkrementieren pro neuem Datensatz).|
|`DateTimeFromUnixSeconds`|Konvertiert eine Zahl, die UNIX-Zeit (Sekunden seit 1970-01-01) darstellt, in die UTC-DateTime|
|`DateTimeFromUnixMilliseconds`|Konvertiert eine Zahl, die UNIX-Zeit (in Millisekunden seit 1970-01-01) darstellt, in die UTC-DateTime|
|`DateTimeFromUnixMicroseconds`|Konvertiert die Zahl, die UNIX-Zeit (Mikrosekunden seit 1970-01-01) darstellt, in die UTC-DateTime|
|`DateTimeFromUnixNanoseconds`|Konvertiert die Zahl, die UNIX-Zeit (Nanosekunden seit 1970-01-01) darstellt, in die UTC-DateTime|
