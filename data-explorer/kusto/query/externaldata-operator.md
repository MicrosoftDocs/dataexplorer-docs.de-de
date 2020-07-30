---
title: 'externaldata-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den externen Daten Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 698acc481a6313160602c939774a6029978df483
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348104"
---
# <a name="externaldata-operator"></a>externaldata-Operator

Der `externaldata` -Operator gibt eine Tabelle zurück, deren Schema in der Abfrage selbst definiert ist und dessen Daten aus einem externen Speicher Element, z. b. einem BLOB in Azure BLOB Storage, gelesen werden.

## <a name="syntax"></a>Syntax

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *storageconnectionstring* `]` [ `with` `(` *Eigenschaft PROP1* `=` *value1* [ `,` ...] `)` ]

## <a name="arguments"></a>Argumente

* *ColumnName*, *ColumnType*: die Argumente definieren das Schema der Tabelle.
  Die Syntax ist identisch mit der Syntax, die beim Definieren einer Tabelle in der [. Create](../management/create-table-command.md)-Tabelle verwendet wird.

* *Storageconnectionstring*: die [Speicher Verbindungs Zeichenfolge](../api/connection-strings/storage.md) beschreibt das Speicher Element, das die zurück zugebende Daten enthält.

* *Eigenschaft PROP1*, *value1*,...: zusätzliche Eigenschaften, die beschreiben, wie die aus dem Speicher abgerufenen Daten interpretiert werden, wie unter Erfassungs [Eigenschaften](../../ingestion-properties.md)aufgeführt.
    * Derzeit unterstützte Eigenschaften: `format` und `ignoreFirstRecord` .
    * Unterstützte Datenformate: alle Erfassungs [Datenformate](../../ingestion-supported-formats.md) werden unterstützt, einschließlich `CSV` , `TSV` , `JSON` , `Parquet` , `Avro` .

> [!NOTE]
> Dieser Operator hat keine Pipeline Eingabe.

## <a name="returns"></a>Gibt zurück

Der- `externaldata` Operator gibt eine Datentabelle des angegebenen Schemas zurück, dessen Daten aus dem angegebenen Speicher Element analysiert wurden, angegeben durch die Speicher Verbindungs Zeichenfolge.

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird gezeigt, wie alle Datensätze in einer Tabelle gesucht `UserID` werden, deren Spalte zu einem bekannten Satz von IDs gehört, der (eine pro Zeile) in einem externen BLOB aufbewahrt wird.
Da auf die Gruppe indirekt von der Abfrage verwiesen wird, kann Sie sehr groß sein.

```kusto
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```

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
> Die Daten Partitionierung wird vom-Operator nicht erkannt `externaldata()` .
