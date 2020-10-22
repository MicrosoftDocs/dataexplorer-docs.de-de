---
title: 'Erstellen eines Event Grid-Abonnements in Ihrem Speicherkonto: Azure Data Explorer'
description: In diesem Artikel wird beschrieben, wie Sie in Ihrem Speicherkonto in Azure Data Explorer ein Event Grid-Abonnement erstellen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 51d5c48fbcd2373ed5cdd1973cc9e53abb26b2de
ms.sourcegitcommit: 58588ba8d1fc5a6adebdce2b556db5bc542e38d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92099492"
---
# <a name="manually-create-resources-for-event-grid-ingestion"></a>Manuelles Erstellen von Ressourcen für die Event Grid-Erfassung

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [Portal: Manuelles Erstellen von Ressourcen](ingest-data-event-grid-manual.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-Vorlage](data-connection-event-grid-resource-manager.md)

Azure Data Explorer ermöglicht die kontinuierliche Erfassung aus Azure Storage (Azure Blob Storage und Azure Data Lake Storage Gen2) über eine [Event Grid-Erfassungspipeline](ingest-data-event-grid-overview.md). In der Event Grid-Erfassungspipeline werden von einem Azure Event Grid-Dienst Ereignisse zur Erstellung oder Umbenennung von Blobs aus einem Speicherkonto über Azure Event Hub an den Azure Data Explorer weitergeleitet.

In diesem Artikel wird beschrieben, wie Sie die Ressourcen, die für die Event Grid-Erfassung benötigt werden, manuell erstellen: Event Grid-Abonnement, Event Hub-Namespace und Event Hub. Der Event Hub-Namespace und die Event Hub-Erstellung sind unter [Voraussetzungen](#prerequisites) beschrieben. Informationen zur Verwendung der automatischen Erstellung dieser Ressourcen beim Definieren der Event Grid-Erfassung finden Sie unter [Erstellen einer Event Grid-Datenverbindung in Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Erstellen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/).
* [Ein Speicherkonto](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
    * Das Abonnement von Event Grid-Benachrichtigungen kann in Azure Storage-Konten für `BlobStorage`, `StorageV2` oder [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction) festgelegt werden.
* [Ein Event Hub-Namespace und Event Hub](/azure/event-hubs/event-hubs-create).

> [!NOTE]
> Die beste Leistung erzielen Sie, wenn Sie alle Ressourcen in der gleichen Region wie den Azure Data Explorer-Cluster erstellen.

## <a name="create-an-event-grid-subscription"></a>Erstellen eines Event Grid-Abonnements
 
1. Wechseln Sie im Azure-Portal zu Ihrem Speicherkonto.
1. Wählen Sie im linken Menü die Option **Ereignisse** > **Ereignisabonnement** aus.

     :::image type="content" source="media/eventgrid/create-event-grid-subscription-1.png" alt-text="Erstellen eines Event Grid-Abonnements":::

1. Geben Sie im Fenster **Ereignisabonnement erstellen** auf der Registerkarte **Einfach** die folgenden Werte an:

    :::image type="content" source="media/eventgrid/create-event-grid-subscription-2.png" alt-text="Erstellen eines Event Grid-Abonnements":::

    |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
    |---|---|---|
    | Name | *test-grid-connection* | Der Name des zu erstellenden Event Grid-Abonnements.|
    | Ereignisschema | *Event Grid-Schema* | Das gewünschte Schema für die Event Grid-Instanz. |
    | Thementyp | *Speicherkonto* | Der Typ des Event Grid-Themas. Wird automatisch eingefügt.|
    | Quellressource | *gridteststorage1* | Der Name Ihres Speicherkontos. Wird automatisch eingefügt.|
    | Name des Systemthemas | *gridteststorage1...* | Das Systemthema, unter dem Ereignisse von Azure Storage veröffentlicht werden. Von diesem Systemthema wird das Ereignis dann an einen Abonnenten weitergeleitet, der Ereignisse empfängt und verarbeitet. Wird automatisch eingefügt.|
    | Nach Ereignistypen filtern | *Blob erstellt* | Gibt an, bei welchen spezifischen Ereignissen eine Benachrichtigung erfolgen soll. Wählen Sie beim Erstellen des Abonnements einen der derzeit unterstützten Typen aus: „Microsoft.Storage.BlobCreated“ oder „Microsoft.Storage.BlobRenamed“|

1. Wählen Sie unter **ENDPUNKTDETAILS** die Option **Event Hubs** aus.

    :::image type="content" source="media/eventgrid/endpoint-details.png" alt-text="Erstellen eines Event Grid-Abonnements":::

1. Klicken Sie auf **Endpunkt auswählen**, und fügen Sie den von Ihnen erstellten Event Hub ein, z. B. *test-hub*.
    
1. Wählen Sie die Registerkarte **Filter** aus, wenn Sie bestimmte Themen nachverfolgen möchten. Legen Sie die Filter für die Benachrichtigungen wie folgt fest:
   
    :::image type="content" source="media/eventgrid/filters-tab.png" alt-text="Erstellen eines Event Grid-Abonnements":::

   1. Wählen Sie **Betrefffilter aktivieren** aus.
   1. Das Feld **Betreff beginnt mit** ist das *Literalpräfix* des Themas (Betreffs). Da das angewendete Muster *startswith* ist, kann es mehrere Container, Ordner oder Blobs umfassen. Platzhalter sind nicht zulässig.
       * Zum Definieren eines Filters für den Blobcontainer legen Sie das Feld wie folgt fest: *`/blobServices/default/containers/[container prefix]`* .
       * Zum Definieren eines Filters für ein Blobpräfix (oder einen Ordner in Azure Data Lake Gen2) legen Sie das Feld wie folgt fest: *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
   1. Das Feld **Betreff endet auf** ist das *literale* Suffix des Blobs. Platzhalter sind nicht zulässig.
   1. Mit dem Feld **Betreffabgleich mit Beachtung der Groß-/Kleinschreibung** wird angegeben, ob bei den Präfix- und Suffixfiltern nach Groß-/Kleinschreibung unterschieden wird.

    Weitere Informationen zum Filtern von Ereignissen finden Sie unter [Blob Storage-Ereignisse](/azure/storage/blobs/storage-blob-event-overview#filtering-events).

1. Klicken Sie auf **Erstellen**

## <a name="next-steps"></a>Nächste Schritte

* Fahren Sie mit dem Setupvorgang fort, und erstellen Sie eine Datenerfassungsverbindung mit Azure Data Explorer über das Azure-Portal: [Erstellen Sie eine Event Grid-Datenverbindung in Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).

* Falls Sie die Event Grid-Erfassung mit den von Ihnen erstellten Ressourcen nicht fortsetzen möchten und die Ressourcen nicht mehr benötigen, sollten Sie dafür eine [Bereinigung](ingest-data-event-grid.md#clean-up-resources) durchführen, um das Anfallen von Kosten zu vermeiden.
