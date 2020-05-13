---
title: 'externaldata-Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den externaldata-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d46a8669c523955f74d3f489c7b10e5b0f7ccef6
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373286"
---
# <a name="externaldata-operator"></a>externaldata-Operator

Der `externaldata` -Operator gibt eine Tabelle zurück, deren Schema in der Abfrage selbst definiert ist und dessen Daten aus einem externen Speicher Element gelesen werden (z. b. ein BLOB in Azure BLOB Storage).

> [!NOTE]
> Dieser Operator hat keine Pipeline Eingabe.

**Syntax**

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *storageconnectionstring* `]` [ `with` `(` *Eigenschaft PROP1* `=` *value1* [ `,` ...] `)` ]

**Argumente**

* *ColumnName*, *ColumnType*: definiert das Schema der Tabelle.
  Die Syntax ist identisch mit der Syntax, die beim Definieren einer Tabelle in der [. Create](../management/create-table-command.md)-Tabelle verwendet wird.

* *Storageconnectionstring*: die [Speicher Verbindungs Zeichenfolge](../api/connection-strings/storage.md) beschreibt das Speicher Element, das die zurück zugebende Daten enthält.

* *Eigenschaft PROP1*, *value1*,...: zusätzliche Eigenschaften, die beschreiben, wie die aus dem Speicher abgerufenen Daten interpretiert werden, wie unter Erfassungs [Eigenschaften](../management/data-ingestion/index.md)aufgeführt.
    * Derzeit unterstützte Eigenschaften: `format` und `ignoreFirstRecord` .
    * Unterstützte Datenformate: alle Erfassungs [Datenformate](../../ingestion-supported-formats.md) werden unterstützt, einschließlich `csv` , `tsv` , `json` , `parquet` , `avro` .

**Rückgabe**

Der- `externaldata` Operator gibt eine Datentabelle des angegebenen Schemas zurück, dessen Daten aus dem angegebenen Speicher Element, das durch die Speicher Verbindungs Zeichenfolge angegeben wurde, analysiert wurden.

**Beispiel**

Im folgenden Beispiel wird gezeigt, wie alle Datensätze in einer Tabelle gesucht `UserID` werden, deren Spalte zu einem bekannten Satz von IDs gehört, der (eine pro Zeile) in einem externen BLOB aufbewahrt wird.
Da die Abfrage indirekt auf den Satz verweist, kann Sie sehr groß sein.

```
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```