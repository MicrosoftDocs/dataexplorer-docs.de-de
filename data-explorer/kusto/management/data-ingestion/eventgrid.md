---
title: 'Erfassung von Speicher mit Event Grid-Abonnement: Azure Daten-Explorer'
description: In diesem Artikel wird die Erfassung von Speicher mithilfe Event Grid Abonnements in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: b8b72c2bc3b34f6d42ea71903272893ffde773fc
ms.sourcegitcommit: 4507466bdcc7dd07e6e2a68c0707b6226adc25af
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87106448"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Erfassung aus dem Speicher mithilfe eines Event Grid-Abonnements

Azure Daten-Explorer bietet eine kontinuierliche Erfassung von Azure Storage (BLOB Storage und ADLSv2) mit [Azure Event Grid](/azure/event-grid/overview) Abonnement für BLOB erstellte Benachrichtigungen und das Streamen dieser Benachrichtigungen an Azure-Daten-Explorer über einen Event Hub.

## <a name="data-format"></a>Datenformat

* Blobdateien können in einem der [unterstützten Formate](../../../ingestion-supported-formats.md)vorliegen.
* Blobblöcke können komprimiert werden. Weitere Informationen finden Sie [unter Unterstützte Komprimierungen](../../../ingestion-supported-formats.md#supported-data-compression-formats).

> [!NOTE]
> Im Idealfall sollte die ursprüngliche unkomprimierte Datengröße Teil der BLOB-Metadaten sein.
> Wenn die nicht komprimierte Größe nicht angegeben ist, wird Sie von Azure Daten-Explorer basierend auf der Dateigröße geschätzt. Sie können die ursprüngliche Datengröße angeben, indem Sie die- `rawSizeBytes` [Eigenschaft](#ingestion-properties) für die BLOB-Metadaten auf die unkomprimierte Datengröße in Bytes festlegen.
> 
> Die Erfassungs Größe der nicht komprimierten Größe ist pro Datei mit 4 GB nicht beschränkt.

## <a name="ingestion-properties"></a>Erfassungseigenschaften

Sie können Erfassungs [Eigenschaften](../../../ingestion-properties.md) der bloberfassung über die BLOB-Metadaten angeben.
Sie können die folgenden Eigenschaften festlegen:

|Eigenschaft | Beschreibung|
|---|---|
| rawsizebytes | Größe der Rohdaten (unkomprimiert). Bei Avro/Orc/Parkett ist dieser Wert die Größe, bevor die Format spezifische Komprimierung angewendet wird.|
| kustotable |  Name der vorhandenen Zieltabelle. Überschreibt die `Table`, die auf dem Blatt `Data Connection` festgelegt ist. |
| kustodataformat |  Datenformat. Überschreibt das **Datenformat** , das auf dem Blatt **Datenverbindung** festgelegt ist. |
| kustoingestionmappingreferenzierung |  Name der zu verwendenden vorhandenen Erfassungszuordnung. Überschreibt die auf dem Blatt **Datenverbindung** festgelegte **Spalten Zuordnung** .|
| kustoignorefirstrecord | Wenn der Wert auf festgelegt `true` ist, ignoriert Azure Daten-Explorer die erste Zeile des BLOBs. Verwenden Sie diese Eigenschaft in Daten in einem Tabellenformat (CSV, TSV oder ähnliche), um die Header zu ignorieren. |
| kustoextenttags | Zeichenfolgendarstellung von [Tags](../extents-overview.md#extent-tagging), die an die resultierende Erweiterung angefügt werden. |
| kustokreationtime |  Überschreibt [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) für das Blob und ist als ISO 8601-Zeichenfolge formatiert. Verwenden Sie dies für einen Abgleich. |

## <a name="events-routing"></a>Ereignis Routing

Wenn Sie eine BLOB Storage-Verbindung mit Azure Daten-Explorer Cluster einrichten, geben Sie die Ziel Tabellen Eigenschaften an:
* Tabellenname
* Datenformat
* mapping

Dieses Setup ist das Standard Routing für Ihre Daten, das manchmal auch als bezeichnet wird `static routing` .
Sie können auch die Ziel Tabellen Eigenschaften für jedes BLOB angeben, indem Sie BLOB-Metadaten verwenden. Die Daten werden dynamisch gemäß den Erfassungs [Eigenschaften](#ingestion-properties)weitergeleitet.

Im folgenden finden Sie ein Beispiel für das Festlegen von Erfassungs Eigenschaften auf die BLOB-Metadaten vor dem Hochladen. Blobdateien werden an verschiedene Tabellen weitergeleitet.

Weitere Informationen zum Generieren von Daten finden Sie unter [Beispielcode](#generating-data).

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Erstellen eines Event Grid-Abonnements in Ihrem Speicherkonto

> [!NOTE]
> Erstellen Sie für eine optimale Leistung alle Ressourcen in der gleichen Region wie der Azure Daten-Explorer-Cluster.

### <a name="prerequisites"></a>Voraussetzungen

* [Informationen zu Azure-Speicherkonten](/azure/storage/common/storage-quickstart-create-account)
  Event Grid Benachrichtigungs Abonnement kann auf Azure Storage Konten für Art oder festgelegt werden `BlobStorage` `StorageV2` .
  Das Aktivieren von [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction) wird ebenfalls unterstützt.
* [Erstellen Sie einen Event Hub](/azure/event-hubs/event-hubs-create).

### <a name="event-grid-subscription"></a>Event Grid-Abonnement
 
1. Suchen Sie im Azure-Portal nach Ihrem Speicherkonto.
1. Wählen Sie im Menü auf der linken Seite **Ereignisse**  >  **Ereignis Abonnement**aus.

     :::image type="content" source="../images/eventgrid/create-event-grid-subscription-1.png" alt-text="Erstellen eines Event Grid-Abonnements":::

1. Geben Sie im Fenster **Ereignisabonnement erstellen** auf der Registerkarte **Einfach** die folgenden Werte an:

    :::image type="content" source="../images/eventgrid/create-event-grid-subscription-2.png" alt-text="Erstellen von Ereignis Abonnement Werten für die Eingabe":::

    |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
    |---|---|---|
    | Name | *test-grid-connection* | Der Name des Event Grid-Abonnements, das Sie erstellen möchten.|
    | Ereignisschema | *Event Grid-Schema* | Das gewünschte Schema für die Event Grid-Instanz. |
    | Thementyp | *Speicherkonto* | Die Art des Event Grid-Themas. |
    | Quell Ressource | *gridteststorage1* | Der Name Ihres Speicherkontos. |
    | Name des Systemthemas | *gridteststorage1...* | Das System Thema, in dem Ereignisse von Azure Storage veröffentlicht werden. Dieses System Thema leitet das Ereignis dann an einen Abonnenten weiter, der Ereignisse empfängt und verarbeitet. |
    | Filtern nach Ereignis Typen | *BLOB erstellt* | Gibt an, bei welchen spezifischen Ereignissen eine Benachrichtigung erfolgen soll. Der derzeit unterstützte Typ ist "Microsoft. Storage. blobcreated". Stellen Sie sicher, dass Sie es beim Erstellen des Abonnements auswählen.|
    | Endpunkttyp | *Event Hubs* | Die Art des Endpunkts, an den Sie die Ereignisse senden. |
    | Endpunkt | *test-hub* | Der von Ihnen erstellte Event Hub. |

1. Wählen Sie die Registerkarte **Filter** aus, wenn Sie bestimmte Themen nachverfolgen möchten. Legen Sie die Filter für die Benachrichtigungen wie folgt fest:
   * Wählen Sie **Betrefffilter aktivieren** aus.
   * Der **Betreff beginnt mit Feld und** ist das *literalpräfix* des Subjekts. Da das angewendete Muster *StartWith*ist, kann es sich über mehrere Container, Ordner oder blobbereiche erstrecken. Platzhalter sind nicht zulässig.
       * Zum Definieren eines Filters für den Blobcontainer *muss* das Feld wie folgt festgelegt werden: *`/blobServices/default/containers/[container prefix]`* .
       * Zum Definieren eines Filters für ein Blobpräfix (oder einen Ordner in Azure Data Lake Gen2) *muss* das Feld wie folgt festgelegt werden: *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
   * Das Feld **Betreff endet auf** ist das *literale* Suffix des Blobs. Platzhalter sind nicht zulässig.
   * Das Feld für die Unterscheidung nach **Groß-/Kleinschreibung** gibt an, ob bei den Präfix-und suffixfiltern
   * Ausführlichere Informationen zum Filtern von Ereignissen finden Sie unter [Blob Storage-Ereignissen](/azure/storage/blobs/storage-blob-event-overview#filtering-events).
    
        :::image type="content" source="../images/eventgrid/filters-tab.png" alt-text="Filter Registerkarte (Ereignis Raster)":::

> [!NOTE]
> Wenn der Endpunkt den Empfang eines Ereignisses nicht anerkennt, aktiviert Azure Event Grid einen Wiederholungs Mechanismus. Wenn diese Wiederholungs Übermittlung fehlschlägt, kann Event Grid die nicht übermittelten Ereignisse *mithilfe eines Prozesses*für unzustellbare Nachrichten an ein Speicherkonto übermitteln. Weitere Informationen finden Sie unter [Event Grid – Nachrichtenübermittlung und -wiederholung](/azure/event-grid/delivery-and-retry#retry-schedule-and-duration).

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Verbindung mit der Datenerfassung mit Azure Daten-Explorer

* Über Azure-Portal: [Erstellen Sie eine Event Grid Datenverbindung in Azure Daten-Explorer](../../../ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).
* Verwenden des Kusto Management .NET SDK: [Hinzufügen einer Event Grid Datenverbindung](../../../data-connection-event-grid-csharp.md#add-an-event-grid-data-connection)
* Verwenden des Kusto Management python SDK: [Hinzufügen einer Event Grid Datenverbindung](../../../data-connection-event-grid-python.md#add-an-event-grid-data-connection)
* Mit [Azure Resource Manager Vorlage zum Hinzufügen einer Event Grid Datenverbindung](../../../data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>Generieren von Daten

> [!NOTE]
> * Verwenden `BlockBlob` Sie, um Daten zu generieren. `AppendBlob` wird nicht unterstützt.

Es folgt ein Beispiel zum Erstellen eines BLOBs aus einer lokalen Datei, Festlegen der Erfassungs Eigenschaften auf die BLOB-Metadaten und Hochladen des BLOBs:

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

> [!NOTE]
> Wenn Sie Azure Data Lake Gen2 Storage SDK verwenden `CreateFile` , müssen Sie zum Hochladen von Dateien verwenden und `Flush` am Ende mit dem close-Parameter auf "true" festlegen.
> Ein ausführliches Beispiel für die ordnungsgemäße Verwendung Data Lake Gen2 SDK finden [Sie unter Hochladen einer Datei mit Azure Data Lake SDK](../../../data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk).

## <a name="blob-lifecycle"></a>BLOB-Lebenszyklus

Azure Daten-Explorer die blobdaten nach der Erfassung nicht löschen. Verwenden Sie den [Azure BLOB Storage-Lebenszyklus](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) zum Verwalten des BLOB-Löschvorgangs. Es wird empfohlen, die blobinformationen für drei bis fünf Tage beizubehalten.

## <a name="known-issues"></a>Bekannte Probleme

* Beachten Sie beim Verwenden von Azure Daten-Explorer zum [exportieren](../data-export/export-data-to-storage.md) der für die Event Grid-Erfassung verwendeten Dateien Folgendes: 
    * Event Grid Benachrichtigungen werden nicht ausgelöst, wenn die für den Export Befehl bereitgestellte Verbindungs Zeichenfolge oder die für eine [externe Tabelle](../data-export/export-data-to-an-external-table.md) angegebene Verbindungs Zeichenfolge eine Verbindungs Zeichenfolge im [ADLS Gen2 Format](../../api/connection-strings/storage.md#azure-data-lake-store) (z `abfss://filesystem@accountname.dfs.core.windows.net` . b.) ist, aber das Speicherkonto für den hierarchischen Namespace nicht aktiviert ist 
    * Wenn das Konto nicht für den hierarchischen Namespace aktiviert ist, muss die Verbindungs Zeichenfolge das [BLOB Storage](../../api/connection-strings/storage.md#azure-storage-blob) Format (z `https://accountname.blob.core.windows.net` . b.) verwenden. Der Export funktioniert wie erwartet, auch wenn die ADLS Gen2 Verbindungs Zeichenfolge verwendet wird, aber es werden keine Benachrichtigungen ausgelöst, und die Event Grid Erfassung funktioniert nicht.
