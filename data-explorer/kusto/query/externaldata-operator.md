---
title: 'externaldata-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den externen Daten Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 5b17b69fd49e937b672da07dd0b5c8abae9100fb
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253045"
---
# <a name="externaldata-operator"></a>externaldata-Operator

Der `externaldata` -Operator gibt eine Tabelle zurück, deren Schema in der Abfrage selbst definiert ist und dessen Daten aus einem externen Speicher Element, z. b. einem BLOB in Azure BLOB Storage oder einer Datei in Azure Data Lake Storage, gelesen werden.

## <a name="syntax"></a>Syntax

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...]`)`   
`[`*Storageconnectionstring* [ `,` ...]`]`   
[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]

## <a name="arguments"></a>Argumente

* *ColumnName*, *ColumnType*: die Argumente definieren das Schema der Tabelle.
  Die Syntax ist identisch mit der Syntax, die beim Definieren einer Tabelle in der [. Create](../management/create-table-command.md)-Tabelle verwendet wird.

* *Storageconnectionstring*: [Speicher Verbindungs](../api/connection-strings/storage.md) Zeichenfolgen, die die Speicher Artefakte beschreiben, die die zurück zugebende Daten enthalten.

* *PropertyName*, *PropertyValue*,...: zusätzliche Eigenschaften, die beschreiben, wie die aus dem Speicher abgerufenen Daten interpretiert werden, wie unter Erfassungs [Eigenschaften](../../ingestion-properties.md)aufgeführt.

Derzeit werden die folgenden Eigenschaften unterstützt:

| Eigenschaft         | type     | BESCHREIBUNG       |
|------------------|----------|-------------------|
| `format`         | `string` | Datenformat. Wenn keine Angabe erfolgt, wird versucht, das Datenformat aus der Dateierweiterung (standardmäßig) zu erkennen `CSV` . Alle Erfassungs [Datenformate](../../ingestion-supported-formats.md) werden unterstützt. |
| `ignoreFirstRecord` | `bool` | Wenn der Wert auf true festgelegt ist, wird angegeben, dass der erste Datensatz in jeder Datei ignoriert wird. Diese Eigenschaft ist hilfreich beim Abfragen von CSV-Dateien mit Headern. |
| `ingestionMapping` | `string` | Ein Zeichen folgen Wert, der angibt, wie Daten aus der Quelldatei den tatsächlichen Spalten im Resultset des Operators zugeordnet werden. Siehe [Daten](../management/mappings.md)Zuordnungen. |


> [!NOTE]
> * Dieser Operator akzeptiert keine Pipeline Eingaben.
> * Standard mäßige [Abfrage Limits](../concepts/querylimits.md) gelten auch für externe Daten Abfragen.

## <a name="returns"></a>Rückgabe

Der- `externaldata` Operator gibt eine Datentabelle des angegebenen Schemas zurück, dessen Daten aus dem angegebenen Speicher Element analysiert wurden, angegeben durch die Speicher Verbindungs Zeichenfolge.

## <a name="examples"></a>Beispiele

**Abrufen einer Liste von Benutzer-IDs, die in gespeichert sind Azure BLOB Storage**

Im folgenden Beispiel wird gezeigt, wie alle Datensätze in einer Tabelle gesucht `UserID` werden, deren Spalte zu einem bekannten Satz von IDs gehört, der in einer externen Speicherdatei (eine pro Zeile) gespeichert ist. Da das Datenformat nicht angegeben wird, ist das erkannte Datenformat `TXT` .

```kusto
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt" 
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```

**Abfragen mehrerer Datendateien**

Im folgenden Beispiel werden mehrere Datendateien abgefragt, die im externen Speicher gespeichert sind.

```kusto
externaldata(Timestamp:datetime, ProductId:string, ProductDescription:string)
[
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00000-7e967c99-cf2b-4dbb-8c53-ce388389470d.csv.gz?...SAS...",
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/02/part-00000-ba356fa4-f85f-430a-8b5a-afd64f128ca4.csv.gz?...SAS...",
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/03/part-00000-acb644dc-2fc6-467c-ab80-d1590b23fc31.csv.gz?...SAS..."
]
with(format="csv")
| summarize count() by ProductId
```

Das obige Beispiel ist eine schnelle Möglichkeit, mehrere Datendateien abzufragen, ohne eine [externe Tabelle](schema-entities/externaltables.md)zu definieren.

> [!NOTE]
> Die Daten Partitionierung wird vom-Operator nicht erkannt `externaldata` .

**Abfragen hierarchischer Datenformate**

Zum Abfragen des hierarchischen Datenformats (z. b. `JSON` , `Parquet` , `Avro` oder) `ORC` `ingestionMapping` muss in den Operator Eigenschaften angegeben werden. In diesem Beispiel wird eine JSON-Datei in Azure BLOB Storage mit folgendem Inhalt gespeichert:

```JSON
{
  "timestamp": "2019-01-01 10:00:00.238521",   
  "data": {    
    "tenant": "e1ef54a6-c6f2-4389-836e-d289b37bcfe0",   
    "method": "RefreshTableMetadata"   
  }   
}   
{
  "timestamp": "2019-01-01 10:00:01.845423",   
  "data": {   
    "tenant": "9b49d0d7-b3e6-4467-bb35-fa420a25d324",   
    "method": "GetFileList"   
  }   
}
...
```

Um diese Datei mit dem- `externaldata` Operator abzufragen, muss eine Datenzuordnung angegeben werden. Die Zuordnung legt fest, wie JSON-Felder den Operatoren von Resultsets zugeordnet werden:

```kusto
externaldata(Timestamp: datetime, TenantId: guid, MethodName: string)
[ 
   h@'https://mycompanystorage.blob.core.windows.net/events/2020/09/01/part-0000046c049c1-86e2-4e74-8583-506bda10cca8.json?...SAS...'
]
with(format='multijson', ingestionMapping='[{"Column":"Timestamp","Properties":{"Path":"$.time"}},{"Column":"TenantId","Properties":{"Path":"$.data.tenant"}},{"Column":"MethodName","Properties":{"Path":"$.data.method"}}]')
```

Das `MultiJSON` Format wird hier verwendet, da einzelne JSON-Datensätze in mehrere Zeilen überspannt werden.

Weitere Informationen zur Zuordnungssyntax finden Sie unter [Datenzuordnungen](../management/mappings.md).
