---
title: infer_storage_schema-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird infer_storage_schema-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 6c4543a3b029017067867bb70d913509941c332e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513905"
---
# <a name="infer_storage_schema-plugin"></a>infer_storage_schema Plugin

Dieses Plug-In leitet das Schema externer Daten ab und gibt es als CSL-Schemazeichenfolge zurück, die beim [Erstellen externer Tabellen](../management/externaltables.md#create-or-alter-external-table)verwendet werden kann.

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

`evaluate` `infer_storage_schema(` *Tastatur* `)`

**Argumente**

Ein einzelnes *Optionsargument* ist `dynamic` ein konstanter Wert des Typs, der einen Eigenschaftenbeutel enthält, der Eigenschaften der Anforderung angibt:

|Name                    |Erforderlich|BESCHREIBUNG|
|------------------------|--------|-----------|
|`StorageContainers`|Ja|Liste der [Speicherverbindungszeichenfolgen,](../api/connection-strings/storage.md) die präfix-URI für gespeicherte Datenartefakte darstellen|
|`DataFormat`|Ja|Eines der [unterstützten Datenformate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).|
|`FileExtension`|Nein|Scannen Sie nur Dateien, die mit dieser Dateierweiterung enden. Es ist nicht erforderlich, aber die Angabe kann den Prozess beschleunigen (oder Probleme beim Lesen von Daten beseitigen).|
|`FileNamePrefix`|Nein|Scannen Sie nur Dateien, die mit diesem Präfix beginnen. Es ist nicht erforderlich, aber die Angabe kann den Prozess beschleunigen|
|`Mode`|Nein|Schemarückschlussstrategie, eine `any`von: `last` `all`, , . Leiten Sie das Datenschema aus jeder (zuerst gefundenen) Datei, aus der zuletzt geschriebenen Datei bzw. aus allen Dateien ab. Standardwert: `last`.|

**Rückgabe**

Das `infer_storage_schema` Plugin gibt eine einzelne Ergebnistabelle zurück, die eine einzelne Zeile/Spalte mit EINER CSL-Schemazeichenfolge enthält.

> [!NOTE]
> * Schema-Inferenzstrategie "all" ist eine sehr "teure" Operation, da sie das Lesen *aller* gefundenen Artefakte und das Zusammenführen ihres Schemas impliziert.
> * Einige zurückgegebene Typen sind möglicherweise nicht die tatsächlichen Typen als Ergebnis falscher Typvermutung (oder aufgrund des Schemazusammenführungsprozesses). Aus diesem Grund wird es dringend gebeten, das Ergebnis sorgfältig zu überprüfen, bevor Sie eine externe Tabelle erstellen.

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

|CslSchema|
|---|
|app_id:string, user_id:long, event_time:datetime, country:string, city:string, device_type:string, device_vendor:string, ad_network:string, campaign:string, site_id:string, event_type:string, event_name:string, organic:string, days_from_install:int, revenue:real|