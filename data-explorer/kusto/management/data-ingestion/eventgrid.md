---
title: 'Erfassung von Speicher mit Event Grid-Abonnement: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Erfassung von Speicher mithilfe Event Grid Abonnements in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 7673b50a8d1ff401f8c2fa086b7eec34c0517238
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373481"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Erfassung aus dem Speicher mithilfe eines Event Grid-Abonnements

Azure Daten-Explorer bietet eine kontinuierliche Erfassung von Azure Storage (BLOB Storage und ADLSv2) mit [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) Abonnement für BLOB erstellte Benachrichtigungen und Streaming dieser Benachrichtigungen über einen Event Hub an Kusto.

## <a name="data-format"></a>Datenformat

* Blobdateien können in einem der [unterstützten Formate](../../../ingestion-supported-formats.md)vorliegen.
* Blobausdrücke können in jedem der [unterstützten Komprimierungen](../../../ingestion-supported-formats.md#supported-data-compression-formats) komprimiert werden.

> [!NOTE]
> Im Idealfall sollte die ursprüngliche unkomprimierte Datengröße Teil der BLOB-Metadaten sein.
> Wenn die unkomprimierte Größe nicht angegeben ist, wird Sie von Azure Daten-Explorer basierend auf der Dateigröße geschätzt. Geben Sie die ursprüngliche Datengröße durch Festlegen der- `rawSizeBytes` [Eigenschaft](#ingestion-properties) für die BLOB-Metadaten auf die **unkomprimierte** Datengröße in Bytes an.
> Beachten Sie, dass die maximale Erfassungs Größe für eine nicht komprimierte Größe pro Datei von 4 GB vorliegt.

## <a name="ingestion-properties"></a>Erfassungseigenschaften

Sie können Erfassungs [Eigenschaften](../../../ingestion-properties.md) der bloberfassung über die BLOB-Metadaten angeben.
Sie können die folgenden Eigenschaften festlegen:

|Eigenschaft | Beschreibung|
|---|---|
| rawsizebytes | Größe der Rohdaten (unkomprimiert). Bei Avro/ORC/Parquet ist dies die Größe vor dem Anwenden der formatspezifischen Komprimierung.|
| kustotable |  Name der vorhandenen Zieltabelle. Überschreibt die `Table`, die auf dem Blatt `Data Connection` festgelegt ist. |
| kustodataformat |  Datenformat. Überschreibt das `Data format`, das auf dem Blatt `Data Connection` festgelegt ist. |
| kustoingestionmappingreferenzierung |  Name der zu verwendenden vorhandenen Erfassungszuordnung. Überschreibt das `Column mapping`, das auf dem Blatt `Data Connection` festgelegt ist.|
| kustoignorefirstrecord | Wenn der Wert auf festgelegt `true` ist, ignoriert Azure Daten-Explorer die erste Zeile des BLOBs. Verwenden Sie diese Eigenschaft in Daten in einem Tabellenformat (CSV, TSV oder ähnliche), um die Header zu ignorieren. |
| kustoextenttags | Zeichenfolgendarstellung von [Tags](../extents-overview.md#extent-tagging), die an die resultierende Erweiterung angefügt werden. |
| kustokreationtime |  Überschreibt [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) für das Blob und ist als ISO 8601-Zeichenfolge formatiert. Verwenden Sie dies für einen Abgleich. |

## <a name="events-routing"></a>Ereignis Routing

Wenn Sie eine BLOB Storage-Verbindung mit Azure Daten-Explorer Cluster einrichten, geben Sie die Ziel Tabellen Eigenschaften an (Tabellenname, Datenformat und Zuordnung). Dies ist das Standard Routing für Ihre Daten, das auch als bezeichnet wird `static routig` .
Sie können auch die Ziel Tabellen Eigenschaften für jedes BLOB angeben, indem Sie BLOB-Metadaten verwenden. Die Daten werden gemäß den Erfassungs [Eigenschaften](#ingestion-properties)dynamisch weitergeleitet.

Im folgenden finden Sie ein Beispiel für das Festlegen von Erfassungs Eigenschaften auf die BLOB-Metadaten vor dem Hochladen. Blobdateien werden an verschiedene Tabellen weitergeleitet.

Weitere Informationen zum Generieren von Daten finden Sie im [Beispielcode](#generating-data) .

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
> Erstellen Sie für eine optimale Leistung alle Ressourcen in der gleichen Region wie der Azure Daten-Explorer-Cluster.

### <a name="prerequisites"></a>Voraussetzungen

* [Informationen zu Azure-Speicherkonten](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) 
  Event Grid Benachrichtigungs Abonnement kann auf Azure Storage Konten der Art oder festgelegt werden `BlobStorage` `StorageV2` . 
  Das Aktivieren von [Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) wird ebenfalls unterstützt.
* [Erstellen Sie einen Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

### <a name="event-grid-subscription"></a>Event Grid-Abonnement

* Kusto `Event Hub` ist als endpoind-Typ ausgewählt, der zum Transportieren von BLOB Storage-Ereignis Benachrichtigungen verwendet wird. `Event Grid schema`ist das ausgewählte Schema für Benachrichtigungen. Beachten Sie, dass jeder beliebige Hub eine Verbindung bedienen kann.
* Die BLOB Storage-Abonnement Verbindung verarbeitet Benachrichtigungen vom Typ `Microsoft.Storage.BlobCreated` . Stellen Sie sicher, dass Sie es beim Erstellen des Abonnements auswählen. Beachten Sie, dass andere Benachrichtigungs Typen, sofern ausgewählt, ignoriert werden.
* Ein Abonnement kann über Speicher Ereignisse in einem oder mehreren Containern benachrichtigt werden. Wenn Sie Dateien aus einem bestimmten Container nachverfolgen möchten, legen Sie die Filter für die Benachrichtigungen wie folgt fest: beim Einrichten einer Verbindung müssen Sie sich an die folgenden Werte kümmern: 
   * Der **Betreff beginnt mit** "Filter" ist das *literalpräfix* des BLOB-Containers. Da das angewendete Muster *startswith* ist, kann es mehrere Container umfassen. Platzhalter sind nicht zulässig.
     Es *muss* wie folgt festgelegt werden: *`/blobServices/default/containers/<prefix>`* . Beispiel: */blobServices/default/Containers/StormEvents-2020-*
   * Das Feld **Betreff endet auf** ist das *literale* Suffix des Blobs. Platzhalter sind nicht zulässig. Nützlich zum Filtern von Dateierweiterungen.

Eine ausführliche Exemplarische Vorgehensweise finden Sie im Handbuch [Erstellen eines Event Grid Abonnements in Ihrem Speicherkonto](../../../ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account) .

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Verbindung mit der Datenerfassung mit Azure Daten-Explorer

* Über das Azure-Portal: [Erstellen Sie eine Event Grid Datenverbindung in Azure Daten-Explorer](../../../ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).
* Verwenden des Kusto Management .NET SDK: [Hinzufügen einer Event Grid Datenverbindung](../../../data-connection-event-grid-csharp.md#add-an-event-grid-data-connection)
* Verwenden des Kusto Management python SDK: [Hinzufügen einer Event Grid Datenverbindung](../../../data-connection-event-grid-python.md#add-an-event-grid-data-connection)
* Mit Arm-Vorlage: [Azure Resource Manager Vorlage zum Hinzufügen einer Event Grid Datenverbindung](../../../data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>Erstellen von Daten

> [!NOTE]
> * Verwenden `BlockBlob` Sie, um Daten zu generieren. `AppendBlob` wird nicht unterstützt.
<!--> * Using ADLSv2 storage requires using `CreateFile` API for uploading blobs and flush at the end. 
    Kusto will get 2 notificatiopns: when blob is created and when stream is flushed. First notification arrives before the data is ready and ignored. Second notification is processed.-->

Im folgenden finden Sie ein Beispiel für das Erstellen eines BLOBs aus einer lokalen Datei, das Festlegen der Erfassungs Eigenschaften auf die BLOB-Metadaten und das Hochladen:

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

## <a name="blob-lifecycle"></a>BLOB-Lebenszyklus

Azure Daten-Explorer löscht die blobdaten nach der Erfassung nicht, behält sie jedoch drei bis fünf Tage bei. Verwenden Sie den [Azure BLOB Storage-Lebenszyklus](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) zum Verwalten des BLOB-Löschvorgangs.