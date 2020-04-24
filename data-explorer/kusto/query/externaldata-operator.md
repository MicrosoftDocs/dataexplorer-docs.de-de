---
title: externer Datenoperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der externe Datenoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0a4a151d1fd052e27e4daf76539ef6dbd9d94c83
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515469"
---
# <a name="externaldata-operator"></a>externaldata-Operator

Der `externaldata` Operator gibt eine Tabelle zurück, deren Schema in der Abfrage selbst definiert ist und deren Daten aus einem externen Speicherartefakt (z. B. einem Blob in Azure Blob Storage) gelesen werden.

> [!NOTE]
> Dieser Operator verfügt nicht über eine Pipelineeingabe.

**Syntax**

`externaldata``(` *ColumnName* `:` *ColumnType* [`,` .] `)` `]` `with` `(` `=` *Value1* `,` *Prop1* *StorageConnectionString* StorageConnectionString [ Prop1 Wert1 [ ...] `[` `)`]

**Argumente**

* *ColumnName*, *ColumnType*: Definieren Sie das Schema der Tabelle.
  Die Syntax ist die gleiche wie die Syntax, die beim Definieren einer Tabelle in [der .create-Tabelle](../management/create-table-command.md)verwendet wird.

* *StorageConnectionString*: Die [Speicherverbindungszeichenfolge](../api/connection-strings/storage.md) beschreibt das Speicherartefakt, das die zurückzugebenden Daten enthält.

* *Prop1*, *Value1*, ...: Zusätzliche Eigenschaften, die beschreiben, wie die aus dem Speicher abgerufenen Daten interpretiert werden, wie unter [Aufnahmeeigenschaften](../management/data-ingestion/index.md)aufgeführt.
    * Derzeit unterstützte `format` Eigenschaften: und `ignoreFirstRecord`.
    * Unterstützte Datenformate: Alle [Erfassungsdatenformate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) werden `csv` `tsv`unterstützt, `parquet` `avro`einschließlich , , `json`, , .

**Rückgabe**

Der `externaldata` Operator gibt eine Datentabelle des angegebenen Schemas zurück, dessen Daten aus dem angegebenen Speicherartefakt analysiert wurden, das durch die Speicherverbindungszeichenfolge angegeben ist.

**Beispiel**

Das folgende Beispiel zeigt, wie Sie `UserID` alle Datensätze in einer Tabelle suchen, deren Spalte in einen bekannten Satz von IDs fällt, die (einer pro Zeile) in einem externen Blob gehalten werden.
Da auf den Satz indirekt von der Abfrage verwiesen wird, kann er sehr groß sein.

```
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```