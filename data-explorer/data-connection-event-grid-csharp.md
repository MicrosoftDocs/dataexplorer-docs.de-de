---
title: Erstellen einer Event Grid-Datenverbindung für Azure Data Explorer mit C#
description: In diesem Artikel erfahren Sie, wie Sie eine Event Grid-Datenverbindung für Azure Data Explorer mit C# erstellen.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/07/2019
ms.openlocfilehash: 3c79d95575e88e7f4d3969ad0cf521703db536e9
ms.sourcegitcommit: c11e3871d600ecaa2824ad78bce9c8fc5226eef9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554705"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Erstellen einer Event Grid-Datenverbindung für Azure Data Explorer mit C#

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-Vorlage](data-connection-event-grid-resource-manager.md)


[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
 In diesem Artikel erstellen Sie eine Event Grid-Datenverbindung für Azure Data Explorer mit C#.

## <a name="prerequisites"></a>Voraussetzungen

* Falls Sie Visual Studio 2019 nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2019 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**.
* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Erstellen eines [Clusters und einer Datenbank](create-cluster-database-csharp.md)
* Erstellen einer [Tabelle und Spaltenzuordnung](./net-sdk-ingest-data.md#create-a-table-on-your-test-cluster)
* Festlegen von [Datenbank- und Tabellenrichtlinien](database-table-policies-csharp.md) (optional)
* Erstellen eines [Speicherkontos mit einem Event Grid-Abonnement](ingest-data-event-grid.md)

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Hinzufügen einer Event Grid-Datenverbindung

Im folgenden Beispiel wird gezeigt, wie eine Event Grid-Datenverbindung programmgesteuert hinzugefügt wird. Weitere Informationen zum Hinzufügen einer Event Grid-Datenverbindung mithilfe des Azure-Portals finden Sie unter [Erstellen einer Event Grid-Datenbankverbindung in Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
var blobStorageEventType = "Microsoft.Storage.BlobCreated";

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat, blobStorageEventType: blobStorageEventType));
```

|**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ihre Mandanten-ID. Wird auch als Verzeichnis-ID bezeichnet.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Abonnement-ID, die Sie für die Ressourcenerstellung verwenden.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Client-ID der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann.|
| clientSecret | *xxxxxxxxxxxxxx* | Das Clientgeheimnis der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann. |
| resourceGroupName | *testrg* | Der Name der Ressourcengruppe, die Ihren Cluster enthält.|
| clusterName | *mykustocluster* | Der Name Ihres Clusters.|
| databaseName | *mykustodatabase* | Der Name der Zieldatenbank in Ihrem Cluster.|
| dataConnectionName | *myeventhubconnect* | Der gewünschte Name Ihrer Datenverbindung.|
| tableName | *StormEvents* | Der Name der Zieltabelle in der Zieldatenbank.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Der Name der Spaltenzuordnung, die mit der Zieltabelle verknüpft ist.|
| dataFormat | *csv* | Das Datenformat der Nachricht.|
| eventHubResourceId | *Ressourcen-ID* | Die Ressourcen-ID Ihres Event Hubs, in dem Event Grid für das Senden von Ereignissen konfiguriert ist. |
| storageAccountResourceId | *Ressourcen-ID* | Die Ressourcen-ID Ihres Speicherkontos, das die Daten für die Erfassung enthält. |
| consumerGroup | *$Default* | Die Consumergruppe Ihres Event Hubs.|
| location | *USA, Mitte* | Der Speicherort der Datenverbindungsressource.|
| blobStorageEventType | *Microsoft.Storage.BlobCreated* | Der Typ des Ereignisses, das die Erfassung auslöst Folgende Ereignisse werden unterstützt: „Microsoft.Storage.BlobCreated“ oder „Microsoft.Storage.BlobRenamed“. Das Umbenennen von Blobs wird nur für ADLSv2-Speicher unterstützt.|

## <a name="generate-sample-data"></a>Generieren von Beispieldaten

Nachdem nun eine Verbindung zwischen Azure Data Explorer und dem Speicherkonto besteht, können Sie Beispieldaten erstellen und in den Speicher hochladen.

> [!NOTE]
> Azure Data Explorer löscht die Blobs nach der Erfassung nicht. Behalten Sie die Blobs für drei bis fünf Tage bei, indem Sie den [Azure Blob Storage-Lebenszyklus](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) zum Verwalten von Bloblöschungen verwenden.

### <a name="upload-file-using-azure-blob-storage-sdk"></a>Hochladen einer Datei mit dem Azure Blob Storage SDK

Mit dem folgenden Codeausschnitt wird ein neuer Container in Ihrem Speicherkonto erstellt, eine vorhandene Datei wird in den Container hochgeladen (als Blob), und anschließend werden die Blobs im Container aufgeführt.

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName = <container_name>;
var blobName = <blob_name>;
var localFileName = <file_to_upload>;
var uncompressedSizeInBytes = <uncompressed_size_in_bytes>;
var mapping = <mappingReference>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", uncompressedSizeInBytes);
blob.Metadata.Add("kustoIngestionMappingReference", mapping);
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

### <a name="upload-file-using-azure-data-lake-sdk"></a>Hochladen einer Datei mit dem Azure Data Lake SDK

Wenn Sie mit Data Lake Storage Gen2 arbeiten, können Sie das [Azure Data Lake SDK](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) verwenden, um Dateien in den Speicher hochzuladen. Der folgende Codeausschnitt nutzt Azure.Storage.Files.DataLake v12.5.0, um ein neues Dateisystem in Azure Data Lake-Speicher zu erstellen und eine lokale Datei mit Metadaten in dieses Dateisystem hochzuladen.

```csharp
var accountName = <storage_account_name>;
var accountKey = <storage_account_key>;
var fileSystemName = <file_system_name>;
var fileName = <file_name>;
var localFileName = <file_to_upload>;
var uncompressedSizeInBytes = <uncompressed_size_in_bytes>;
var mapping = <mapping_reference>;

var sharedKeyCredential = new StorageSharedKeyCredential(accountName, accountKey);
var dfsUri = "https://" + accountName + ".dfs.core.windows.net";
var dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), sharedKeyCredential);

// Create the filesystem
var dataLakeFileSystemClient = dataLakeServiceClient.CreateFileSystem(fileSystemName).Value;

// Define metadata
IDictionary<String, String> metadata = new Dictionary<string, string>();
metadata.Add("rawSizeBytes", uncompressedSizeInBytes);
metadata.Add("kustoIngestionMappingReference", mapping);

// Set uploading options
var uploadOptions = new DataLakeFileUploadOptions
{
    Metadata = metadata,
    Close = true // Note: The close option triggers the event being processed by the data connection
};

// Write to the file
var dataLakeFileClient = dataLakeFileSystemClient.GetFileClient(fileName);
dataLakeFileClient.Upload(localFileName, uploadOptions);
```

> [!NOTE]
> Wenn Sie das [Azure Data Lake SDK](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) zum Hochladen einer Datei verwenden, löst die Dateierstellung ein Event Grid-Ereignis mit der Größe 0 aus, und dieses Ereignis wird von Azure Data Explorer ignoriert. Durch den Dateileerungsvorgang wird ein anderes Ereignis ausgelöst, wenn der Parameter *Close* auf *true* festgelegt ist. Dieses Ereignis gibt an, dass dies das endgültige Update ist und der Dateistream geschlossen wurde. Dieses Ereignis wird von der Event Grid-Datenverbindung verarbeitet. Im obigen Codeausschnitt löst die Uploadmethode das Leeren aus, wenn der Dateiupload abgeschlossen ist. Daher muss der auf *true* festgelegte Parameter *Close* definiert werden. Weitere Informationen zum Leeren finden Sie unter [Methode zum Leeren in Azure Data Lake](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flush).

### <a name="rename-file-using-azure-data-lake-sdk"></a>Umbenennen einer Datei mit dem Azure Data Lake SDK

Der folgende Codeausschnitt verwendet das [Azure Data Lake SDK](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/), um ein Blob in einem ADLSv2-Speicherkonto umzubenennen.

```csharp
var accountName = <storage_account_name>;
var accountKey = <storage_account_key>;
var fileSystemName = <file_system_name>;
var sourceFilePath = <source_file_path>;
var destinationFilePath = <destination_file_path>;

var sharedKeyCredential = new StorageSharedKeyCredential(accountName, accountKey);
var dfsUri = "https://" + accountName + ".dfs.core.windows.net";
var dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), sharedKeyCredential);

// Get a client to the the filesystem
var dataLakeFileSystemClient = dataLakeServiceClient.GetFileSystemClient(fileSystemName);

// Rename a file in the file system
var dataLakeFileClient = dataLakeFileSystemClient.GetFileClient(sourceFilePath);
dataLakeFileClient.Rename(destinationFilePath);
```

> [!NOTE]
> * Das Umbenennen von Verzeichnissen ist in ADLSv2 möglich, löst jedoch keine Ereignisse zur *Umbenennung von Blobs* und keine Erfassung von Blobs innerhalb des Verzeichnisses aus. Wenn Sie Blobs nach dem Umbenennen erfassen möchten, benennen Sie die gewünschten Blobs direkt um.
> * Wenn Sie Filter zum Nachverfolgen bestimmter Themen beim [Erstellen der Datenverbindung](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) oder beim [manuellen Erstellen von Event Grid-Ressourcen](ingest-data-event-grid-manual.md#create-an-event-grid-subscription) festgelegt haben, werden diese Filter auf den Zieldateipfad angewendet.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
