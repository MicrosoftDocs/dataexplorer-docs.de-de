---
title: Mit Dem Event Grid-Abonnement vom Speicher erfassen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird beschrieben, wie Sie das Ereignisrasterabonnement in Azure Data Explorer mithilfe eines Event Grid-Abonnements speichern.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 682c39b11292c7e198dba46dad9b5bfa3b520c0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521521"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Vom Speicher mit Event Grid-Abonnement erfassen

Azure Data Explorer bietet kontinuierliche Erfassung aus Azure Storage (Blobstorage und ADLSv2) mit [Azure Event Grid-Abonnement](https://docs.microsoft.com/azure/event-grid/overview) für Blob erstellte Benachrichtigungen und Streaming dieser Benachrichtigungen an Kusto über einen Event Hub.

## <a name="data-format"></a>Datenformat

* Blobs können in jedem der [unterstützten Formate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)vorliegen.
* Blobs können in jeder der [unterstützten Komprimierungen](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats#supported-data-compression-formats) komprimiert werden

> [!NOTE]
> Idealerweise sollte die ursprüngliche unkomprimierte Datengröße Teil der Blobmetadaten sein.
> Wenn die nicht komprimierte Größe nicht angegeben ist, schätzt Azure Data Explorer diese basierend auf der Dateigröße. Stellen Sie die ursprüngliche `rawSizeBytes` Datengröße bereit, indem Sie die [Eigenschaft](#ingestion-properties) für die Blobmetadaten auf **unkomprimierte** Datengröße in Bytes festlegen.
> Bitte beachten Sie, dass es eine unkomprimierte Größenbeschränkung pro Datei von 4 GB gibt.

## <a name="ingestion-properties"></a>Erfassungseigenschaften

Sie können [die Aufnahmeeigenschaften](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) der Blobaufnahme über die Blobmetadaten angeben.
Sie können die folgenden Eigenschaften festlegen:

|Eigenschaft | Beschreibung|
|---|---|
| rawSizeBytes | Größe der unkomprimierten Rohdaten (unkomprimiert). Bei Avro/ORC/Parquet ist dies die Größe, bevor die formatspezifische Komprimierung angewendet wird.|
| kustoTable |  Name der vorhandenen Zieltabelle. Überschreibt `Table` den Satz `Data Connection` auf dem Blatt. |
| kustoDataFormat |  Datenformat. Überschreibt `Data format` den Satz `Data Connection` auf dem Blatt. |
| kustoIngestionMappingReference |  Name der vorhandenen Erfassungszuordnung, die verwendet werden soll. Überschreibt `Column mapping` den Satz `Data Connection` auf dem Blatt.|
| kustoIgnoreFirstRecord | Wenn auf `true`festgelegt, ignoriert Azure Data Explorer die erste Zeile des Blobs. Verwenden Sie in tabellenartigen Daten (CSV, TSV oder ähnliches), um Header zu ignorieren. |
| kustoExtentTags | Zeichenfolge, die [Tags](../extents-overview.md#extent-tagging) darstellt, die mit der resultierenden Ausdehnung verbunden werden. |
| kustoCreationTime |  Überschreibt [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) für das Blob, das als ISO 8601-Zeichenfolge formatiert ist. Zum Nachfüllen verwenden. |

## <a name="events-routing"></a>Ereignisrouting

Geben Sie beim Einrichten einer Blobspeicherverbindung mit dem Azure Data Explorer-Cluster die Eigenschaften der Zieltabelle (Tabellenname, Datenformat und Zuordnung) an. Dies ist das Standardrouting für Ihre `static routig`Daten, auch als bezeichnet.
Sie können auch Die Zieltabelleneigenschaften für jedes Blob mithilfe von Blobmetadaten angeben. Die Daten werden dynamisch weitergeleitet, wie durch [die Aufnahmeeigenschaften](#ingestion-properties)angegeben.

Im Folgenden finden Sie ein Beispiel für das Festlegen von Aufnahmeeigenschaften auf die Blobmetadaten vor dem Hochladen. Blobs werden an verschiedene Tabellen weitergeleitet.

Weitere Informationen zum Generieren von Daten finden Sie im [Beispielcode.](#generating-data)

 ```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="create-event-grid-subscription"></a>Event Grid-Abonnement erstellen

> [!Note]
> Um eine optimale Leistung zu erzielen, erstellen Sie alle Ressourcen in derselben Region wie der Azure Data Explorer-Cluster.

### <a name="prerequisites"></a>Voraussetzungen

* [Informationen zu Azure-Speicherkonten](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) 
  Das Ereignisrasterbenachrichtigungsabonnement kann für Azure `BlobStorage` `StorageV2`Storage-Konten oder festgelegt werden. 
  Das Aktivieren von [Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) wird ebenfalls unterstützt.
* [Erstellen Sie einen Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

### <a name="event-grid-subscription"></a>Event Grid-Abonnement

* Kusto `Event Hub` wurde als Endpoind-Typ ausgewählt, der zum Transport von Blobspeicherereignisbenachrichtigungen verwendet wird. `Event Grid schema`ist das ausgewählte Schema für Benachrichtigungen. Beachten Sie, dass jeder Even Hub eine Verbindung bedienen kann.
* Die Blobspeicherabonnementverbindung verarbeitet Benachrichtigungen `Microsoft.Storage.BlobCreated`vom Typ . Stellen Sie sicher, dass Sie es beim Erstellen des Abonnements auswählen. Beachten Sie, dass andere Arten von Benachrichtigungen, sofern diese ausgewählt sind, ignoriert werden.
* Ein Abonnement kann über Speicherereignisse in einem oder mehreren Containern benachrichtigen. Wenn Sie Dateien aus einem bestimmten Container nachverfolgen möchten, legen Sie die Filter für die Benachrichtigungen wie folgt fest: Achten Sie beim Einrichten einer Verbindung auf die folgenden Werte: 
   * **Subject Begins With** filter ist das *Literalpräfix* des Blobcontainers. Da das angewendete Muster *startswith* ist, kann es mehrere Container umfassen. Platzhalter sind nicht zulässig.
     Sie *muss* wie folgt *`/blobServices/default/containers/<prefix>`* festgelegt werden: . Beispiel: */blobServices/default/containers/StormEvents-2020-*
   * Das Feld **Betreff endet auf** ist das *literale* Suffix des Blobs. Platzhalter sind nicht zulässig. Nützlich zum Filtern von Dateierweiterungen.

Eine ausführliche Vorgehensweise finden Sie im Handbuch [zum Erstellen eines Event Grid-Abonnements in Ihrem Speicherkontohandbuch.](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-grid#create-an-event-grid-subscription-in-your-storage-account)

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Datenerfassungsverbindung mit Azure Data Explorer

* Über Azure Portal: [Erstellen einer Ereignisrasterdatenverbindung in Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-grid#create-an-event-grid-data-connection-in-azure-data-explorer).
* Verwenden von Kusto Management .NET SDK: [Hinzufügen einer Event Grid-Datenverbindung](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-csharp#add-an-event-grid-data-connection)
* Verwenden von Kusto-Verwaltungs-Python-SDK: [Hinzufügen einer Event Grid-Datenverbindung](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-python#add-an-event-grid-data-connection)
* Mit ARM-Vorlage: [Azure Resource Manager-Vorlage zum Hinzufügen einer Event Grid-Datenverbindung](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-resource-manager#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>Generieren von Daten

> [!NOTE]
> * Verwenden `BlockBlob` Sie diese, um Daten zu generieren. `AppendBlob` wird nicht unterstützt.
<!--> * Using ADLSv2 storage requires using `CreateFile` API for uploading blobs and flush at the end. 
    Kusto will get 2 notificatiopns: when blob is created and when stream is flushed. First notification arrives before the data is ready and ignored. Second notification is processed.-->

Im Folgenden finden Sie ein Beispiel für das Erstellen eines Blobs aus einer lokalen Datei, das Festlegen von Aufnahmeeigenschaften auf die Blobmetadaten und das Hochladen:

 ```csharp
 var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set ingestion properties in blob's metadata & uploading the blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIgnoreFirstRecord", "true"); // First line of this csv file are headers
blob.UploadFromFile(csvCompressedLocalFileName);
```

## <a name="blob-lifecycle"></a>Blob-Lebenszyklus

Azure Data Explorer löscht die Blobs nach der Einnahme nicht, sondern behält sie drei bis fünf Tage lang bei. Verwenden Sie [den Azure Blob-Speicherlebenszyklus,](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) um das Bloblöschen zu verwalten.