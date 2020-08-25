---
title: 'Erfassen von Daten aus dem Speicher mit einem Event Grid-Abonnement: Azure Data Explorer'
description: In diesem Artikel wird beschrieben, wie Sie mit einem Event Grid-Abonnement Daten aus dem Speicher in Azure Data Explorer erfassen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: e815a008c219c0eb1eeede8df07817d872ca1fed
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202413"
---
# <a name="connect-to-event-grid"></a>Herstellen einer Verbindung mit Event Grid

Event Grid ist eine Pipeline, die auf Azure Storage lauscht und Azure Data Explorer aktualisiert, um Informationen zu pullen, wenn abonnierte Ereignisse auftreten. Azure Data Explorer ermöglicht eine kontinuierliche Erfassung aus Azure Storage (Blobspeicher und ADLSv2) mit einem [Azure Event Grid](/azure/event-grid/overview)-Abonnement für Benachrichtigungen zur Erstellung von Blobs und das Streaming dieser Benachrichtigungen an Azure Data Explorer über einen Event Hub.

Die Event Grid-Erfassungspipeline umfasst mehrere Schritte. Sie erstellen eine Zieltabelle in Azure Data Explorer, in der die [Daten in einem bestimmten Format](#data-format) erfasst werden. Anschließend erstellen Sie eine Event Grid-Datenverbindung in Azure Data Explorer. Die Event Grid-Datenverbindung muss über Informationen zum [Ereignisrouting](#set-events-routing) verfügen, z. B. die Tabelle, an die Daten gesendet werden sollen, und die Tabellenzuordnung. Außerdem geben Sie [Erfassungseigenschaften](#set-ingestion-properties) an, die die zu erfassenden Daten, die Zieltabelle und die Zuordnung beschreiben. Dieser Prozess kann über das [Azure-Portal](ingest-data-event-grid.md), programmgesteuert mit [C#](data-connection-event-grid-csharp.md) oder [Python](data-connection-event-grid-python.md) oder mit der [Azure Resource Manager-Vorlage](data-connection-event-grid-resource-manager.md) verwaltet werden.

## <a name="data-format"></a>Datenformat

* Siehe [Unterstützte Formate](ingestion-supported-formats.md).
* Siehe [Unterstützte Komprimierungen](ingestion-supported-formats.md#supported-data-compression-formats).
  * Die ursprüngliche Größe der nicht komprimierten Daten sollte Teil der Blobmetadaten sein. Andernfalls wird sie von Azure Data Explorer geschätzt.  Das Größenlimit für die Erfassung von nicht komprimierten Daten pro Datei ist 4 GB.
 
## <a name="set-ingestion-properties"></a>Festlegen von Erfassungseigenschaften

Sie können [Erfassungseigenschaften](ingestion-properties.md) für die Bloberfassung über die Blobmetadaten angeben.
Sie können die folgenden Eigenschaften festlegen:

[!INCLUDE [ingestion-properties-event-grid](includes/ingestion-properties-event-grid.md)]

## <a name="set-events-routing"></a>Festlegen des Ereignisroutings

Beim Einrichten einer Blob Storage-Verbindung mit einem Azure Data Explorer-Cluster geben Sie Eigenschaften für die Zieltabelle an:
* Tabellenname
* Datenformat
* mapping

Diese Einrichtung ist das Standardrouting für Ihre Daten und wird mitunter auch als `static routing` bezeichnet.
Sie können mithilfe von Blobmetadaten auch Zieltabelleneigenschaften für jeden Blob angeben. Die Daten werden wie mit den [Erfassungseigenschaften](#set-ingestion-properties) festgelegt dynamisch weitergeleitet.

Das folgende Beispiel zeigt, wie Sie vor dem Hochladen Erfassungseigenschaften für die Blobmetadaten festlegen. Blobs werden an verschiedene Tabellen weitergeleitet.

Weitere Informationen finden Sie unter [Generieren von Daten](#generate-data).

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

### <a name="generate-data"></a>Generieren von Daten

> [!NOTE]
> Verwenden Sie `BlockBlob`, um Daten zu generieren. `AppendBlob` wird nicht unterstützt.

Sie können wie folgt einen Blob aus einer lokalen Datei erstellen, Erfassungseigenschaften für die Blobmetadaten festlegen und den Blob hochladen:

 ```csharp
 var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Create the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set ingestion properties in blob metadata and upload the blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIgnoreFirstRecord", "true"); // First line of this csv file are headers
blob.UploadFromFile(csvCompressedLocalFileName);
```

> [!NOTE]
> Die Verwendung des Azure Data Lake Gen2 Storage SDK erfordert, dass Sie `CreateFile` zum Hochladen von Dateien verwenden sowie am Ende `Flush` ausführen und dabei den Parameter „close“ auf „true“ festlegen.
> Ein ausführliches Beispiel zur korrekten Verwendung des Data Lake Gen2 SDK finden Sie unter [Hochladen einer Datei mit dem Azure Data Lake SDK](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk).

## <a name="delete-blobs-using-storage-lifecycle"></a>Löschen von Blobs mithilfe des Speicherlebenszyklus

Azure Data Explorer löscht Blobs nach der Erfassung nicht. Informationen zum Löschen von Blobs finden Sie unter [Azure Blob Storage-Lebenszyklus](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal). Es wird empfohlen, die Blobs für drei bis fünf Tage beizubehalten.

## <a name="known-event-grid-issues"></a>Bekannte Probleme mit Event Grid

* Beachten Sie Folgendes, wenn Sie Azure Data Explorer zum [Exportieren](kusto/management/data-export/export-data-to-storage.md) der Dateien für die Event Grid-Erfassung verwenden: 
    * Event Grid-Benachrichtigungen werden nicht ausgelöst, wenn die für den Exportbefehl oder für eine [externe Tabelle](kusto/management/data-export/export-data-to-an-external-table.md) angegebene Verbindungszeichenfolge eine Verbindungszeichenfolge im [ADLS Gen2-Format](kusto/api/connection-strings/storage.md#azure-data-lake-store) ist (z. B. `abfss://filesystem@accountname.dfs.core.windows.net`), das Speicherkonto aber nicht für den hierarchischen Namespace aktiviert ist. 
    * Wenn das Konto nicht für den hierarchischen Namespace aktiviert ist, muss die Verbindungszeichenfolge dem [Blob Storage](kusto/api/connection-strings/storage.md#azure-storage-blob)-Format entsprechen (z. B. `https://accountname.blob.core.windows.net`). Der Export funktioniert auch bei Verwendung der ADLS Gen2-Verbindungszeichenfolge wie erwartet, es werden jedoch keine Benachrichtigungen ausgelöst, und die Event Grid-Erfassung erfolgt nicht.

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen von Blobs in Azure Data Explorer durch das Abonnieren von Event Grid-Benachrichtigungen](ingest-data-event-grid.md)
* [Erstellen einer Event Hub-Datenverbindung für Azure Data Explorer mit C#](data-connection-event-hub-csharp.md)
* [Erstellen einer Event Grid-Datenverbindung für Azure Data Explorer mit Python](data-connection-event-grid-python.md)
* [Erstellen einer Event Grid-Datenverbindung für Azure Data Explorer mit einer Azure Resource Manager-Vorlage](data-connection-event-grid-resource-manager.md)