---
title: 'infer_storage_schema-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird infer_storage_schema-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 1b4a917101ad3a35f8fdbc1cccb257b6f3724b69
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224866"
---
# <a name="infer_storage_schema-plugin"></a>Plug-in infer_storage_schema

Dieses Plug-in leitet das Schema externer Daten ab und gibt es als CSL-Schema Zeichenfolge zurück. Die Zeichenfolge kann beim [Erstellen externer Tabellen](../management/external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table)verwendet werden.

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
  ],
  'DataFormat': 'parquet',
  'FileExtension': '.parquet'
});
evaluate infer_storage_schema(options)
```

**Syntax**

`evaluate` `infer_storage_schema(` *Optionen* `)`

**Argumente**

Ein einzelnes *options* Argument ist ein konstanter Wert des Typs `dynamic` , der einen Eigenschaften Behälter enthält, der die Eigenschaften der Anforderung angibt:

|Name                    |Erforderlich|BESCHREIBUNG|
|------------------------|--------|-----------|
|`StorageContainers`|Ja|Liste der [Speicher Verbindungs](../api/connection-strings/storage.md) Zeichenfolgen, die den Präfix-URI für gespeicherte Daten Artefakte darstellen|
|`DataFormat`|Ja|Eines der unterstützten [Datenformate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).|
|`FileExtension`|Nein|Nur Dateien überprüfen, die mit dieser Dateierweiterung enden. Dies ist nicht erforderlich, aber die Angabe kann den Prozess beschleunigen (oder Daten Leseprobleme vermeiden).|
|`FileNamePrefix`|Nein|Nur Dateien überprüfen, die mit diesem Präfix beginnen. Dies ist nicht erforderlich, aber die Angabe kann den Prozess beschleunigen.|
|`Mode`|Nein|Strategie für die Schema Ableitung, eine der folgenden: `any` , `last` , `all` . Ableiten Sie das Datenschema aus einer beliebigen (ersten gefundenen) Datei, aus der letzten geschriebenen Datei bzw. aus allen Dateien. Standardwert: `last`.|

**Rückgabe**

Das `infer_storage_schema` Plug-in gibt eine einzelne Ergebnistabelle zurück, die eine einzelne Zeile/Spalte mit der CSL-Schema Zeichenfolge enthält

> [!NOTE]
> * Die Strategie "All" für die Schema Rückschluss Strategie ist ein sehr kostengünstiger Vorgang, da Sie das Lesen aus *allen* gefundenen Artefakten und das Zusammenführen Ihres Schemas impliziert.
> * Einige zurückgegebene Typen sind möglicherweise nicht die tatsächlichen Typen, die durch einen falschen Typ (oder als Ergebnis des Schema Zusammenführung Prozesses) verursacht werden. Aus diesem Grund sollten Sie das Ergebnis sorgfältig überprüfen, bevor Sie eine externe Tabelle erstellen.

**Beispiel**

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/MovileEvents/2015;secretKey'
  ],
  'FileExtension': '.parquet',
  'FileNamePrefix': 'part-',
  'DataFormat': 'parquet'
});
evaluate infer_storage_schema(options)
```

*Ergebnis*

|Cslschema|
|---|
|app_id: String, user_id: Long, event_Time: DateTime, Country: String, City: String, device_type: String, device_vendor: String, ad_network: String, Kampagne: String, site_id: String, event_type: String, event_name: String, Organic: String, days_from_install: int, Revenue: Real|