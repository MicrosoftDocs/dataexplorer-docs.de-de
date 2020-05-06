---
title: Erfassen von Azure-Blobs in Azure Data Explorer
description: In diesem Artikel erfahren Sie, wie Sie Speicherkontodaten unter Verwendung eines Event Grid-Abonnements an Azure Data Explorer senden.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 69779e42f14a1dfc512d8752f2cc9989897b2cc6
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741969"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Erfassen von Blobs in Azure Data Explorer durch das Abonnieren von Event Grid-Benachrichtigungen

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-Vorlage](data-connection-event-grid-resource-manager.md)

Azure Data Explorer ist ein schneller und skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Er bietet eine kontinuierliche Erfassung (Laden von Daten) aus Blobs, die in Blobcontainer geschrieben werden. 

In diesem Artikel wird beschrieben, wie Sie ein [Azure Event Grid](/azure/event-grid/overview)-Abonnement festlegen und Ereignisse per Event Hub an Azure Data Explorer leiten. Zunächst benötigen Sie ein Speicherkonto mit einem Event Grid-Abonnement, das Benachrichtigungen an Azure Event Hubs sendet. Anschließend erstellen Sie eine Event Grid-Datenverbindung und zeigen den Datenfluss im gesamten System an.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Erstellen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/).
* [Ein Cluster und eine Datenbank](create-cluster-database-portal.md).
* [Ein Speicherkonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Einen Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Erstellen eines Event Grid-Abonnements in Ihrem Speicherkonto

1. Suchen Sie im Azure-Portal nach Ihrem Speicherkonto.
1. Wählen Sie **Ereignisse** > **Ereignisabonnement**.

    ![Anwendungslink „Abfrage“](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. Geben Sie im Fenster **Ereignisabonnement erstellen** auf der Registerkarte **Einfach** die folgenden Werte an:

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Name | *test-grid-connection* | Der Name der zu erstellenden Event Grid-Instanz.|
    | Ereignisschema | *Event Grid-Schema* | Das gewünschte Schema für die Event Grid-Instanz. |
    | Thementyp | *Speicherkonto* | Die Art des Event Grid-Themas. |
    | Themenressource | *gridteststorage* | Der Name Ihres Speicherkontos. |
    | Alle Ereignistypen abonnieren | *Löschen* | Keine Benachrichtigungen für alle Ereignisse. |
    | Definierte Ereignistypen | *Blob erstellt* | Gibt an, bei welchen spezifischen Ereignissen eine Benachrichtigung erfolgen soll. |
    | Endpunkttyp | *Event Hubs* | Die Art des Endpunkts, an den Sie die Ereignisse senden. |
    | Endpunkt | *test-hub* | Der von Ihnen erstellte Event Hub. |
    | | |

1. Wählen Sie die Registerkarte **Filter** aus, wenn Sie Dateien aus einem bestimmten Container nachverfolgen möchten. Legen Sie die Filter für die Benachrichtigungen wie folgt fest:
    * Das Feld **Betreff beginnt mit** ist das *Literalpräfix* des Blobcontainers. Da das angewendete Muster *startswith* ist, kann es mehrere Container umfassen. Platzhalter sind nicht zulässig.
        * Zum Definieren eines Filters für den Blobcontainer *muss* das Feld wie folgt festgelegt werden: *`/blobServices/default/containers/[container prefix]`* . 
        * Zum Definieren eines Filters für ein Blobpräfix (oder einen Ordner in ADLS Gen2) *muss* das Feld wie folgt festgelegt werden: *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* . 
    * Das Feld **Betreff endet auf** ist das *literale* Suffix des Blobs. Platzhalter sind nicht zulässig.
    * Ausführlichere Informationen zum Filtern von Ereignissen finden Sie unter [Blob Storage-Ereignissen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview#filtering-events). 
    
## <a name="create-a-target-table-in-azure-data-explorer"></a>Erstellen einer Zieltabelle im Azure-Daten-Explorer

Erstellen Sie in Azure Data Explorer eine Tabelle, an die Event Hubs Daten senden kann. Erstellen Sie die Tabelle in dem Cluster und der Datenbank, den bzw. die Sie unter „Voraussetzungen“ vorbereitet haben.

1. Wählen Sie im Azure-Portal unter Ihrem Cluster die Option **Abfrage** aus.

    ![Anwendungslink „Abfrage“](media/ingest-data-event-grid/query-explorer-link.png)

1. Kopieren Sie den folgenden Befehl in das Fenster, und wählen Sie **Ausführen**, um die Tabelle (TestTable) zu erstellen, die die erfassten Daten erhalten soll.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Abfrage erstellen, „Ausführen“](media/ingest-data-event-grid/run-create-table.png)

1. Kopieren Sie den folgenden Befehl in das Fenster, und wählen Sie **Ausführen**, um die eingehenden JSON-Daten den Spaltennamen und Datentypen der Tabelle (TestTable) zuzuordnen.

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Erstellen einer Event Grid-Datenverbindung in Azure Data Explorer

Stellen Sie nun über Azure Data Explorer eine Verbindung mit der Event Grid-Instanz her, damit im Blobcontainer eingehende Daten an die Testtabelle gestreamt werden. 

1. Wählen Sie auf der Symbolleiste die Option **Benachrichtigungen** aus, um zu überprüfen, ob die Bereitstellung des Event Hubs erfolgreich verlaufen ist.

1. Wählen Sie unter dem von Ihnen erstellten Cluster **Datenbanken** > **TestDatabase**.

    ![Testdatenbank auswählen](media/ingest-data-event-grid/select-test-database.png)

1. Wählen Sie **Datenerfassung** > **Datenverbindung hinzufügen**.

    ![Datenerfassung](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Wählen Sie den Verbindungstyp aus: **Blobspeicher**.

1. Füllen Sie das Formular mit den folgenden Informationen aus, und wählen Sie **Erstellen**.

    ![Event Hub-Verbindung](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Datenquelle:

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Name der Datenverbindung | *test-hub-connection* | Der Name der Verbindung, die Sie im Azure Data Explorer erstellen möchten.|
    | Speicherkontoabonnement | Ihre Abonnement-ID | Die Abonnement-ID, unter der sich Ihr Speicherkonto befindet.|
    | Speicherkonto | *gridteststorage* | Der Name des zuvor erstellten Speicherkontos.|
    | Event Grid | *test-grid-connection* | Der Name der erstellten Event Grid-Instanz. |
    | Event Hub-Name | *test-hub* | Der von Ihnen erstellte Event Hub. Dieses Feld wird automatisch ausgefüllt, wenn Sie eine Event Grid-Instanz auswählen. |
    | Consumergruppe | *test-group* | Die Consumergruppe, die im von Ihnen erstellten Event Hub definiert ist. |
    | | |

    Zieltabelle:

     **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Tabelle | *TestTable* | Die Tabelle, die Sie unter **TestDatabase** erstellt haben. |
    | Datenformat | *JSON* | Unterstützte Formate: Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV, RAW und TXT. Unterstützte Komprimierungsoptionen: Zip und gzip |
    | Spaltenzuordnung | *TestMapping* | Die Zuordnung, die Sie in **TestDatabase** erstellt haben, um eingehende JSON-Daten den Spaltennamen und Datentypen von **TestTable** zuzuordnen.|
    | | |
    
## <a name="generate-sample-data"></a>Generieren von Beispieldaten

Nachdem nun eine Verbindung zwischen Azure Data Explorer und dem Speicherkonto besteht, können Sie Beispieldaten erstellen und in den Blobspeicher hochladen.

Wir verwenden ein kleines Shellskript, das einige grundlegenden Azure CLI-Befehle für die Interaktion mit Azure Storage-Ressourcen aufruft. Mit diesem Skript wird ein neuer Container in Ihrem Speicherkonto erstellt, eine vorhandene Datei wird in den Container hochgeladen (als Blob), und anschließend werden die Blobs im Container aufgeführt. Das Skript kann per [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) direkt im Portal ausgeführt werden.

Speichern Sie die Daten in einer Datei, und laden Sie sie mit diesem Skript hoch:

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> Für eine optimale Leistung bei der Erfassung muss die *unkomprimierte* Größe der für die Erfassung übertragenen komprimierten Blobs übermittelt werden. Da Event Grid-Benachrichtigungen nur grundlegende Details enthalten, müssen die Größeninformationen explizit übermittelt werden. Die Informationen zur unkomprimierten Größe können durch Festlegen der `rawSizeBytes`-Eigenschaft in den Blobmetadaten angegeben werden, wobei die Größenangabe der *unkomprimierten* Daten in Byte erfolgt.

### <a name="ingestion-properties"></a>Erfassungseigenschaften

Sie können die [Datenerfassungseigenschaften](ingestion-properties.md) für die Bloberfassung über die Blobmetadaten angeben.

Die folgenden Eigenschaften können festgelegt werden:

|**Eigenschaft** | **Beschreibung der Eigenschaft**|
|---|---|
| `rawSizeBytes` | Größe der Rohdaten (unkomprimiert). Bei Avro/ORC/Parquet ist dies die Größe vor dem Anwenden der formatspezifischen Komprimierung.|
| `kustoTable` |  Name der vorhandenen Zieltabelle. Überschreibt die `Table`, die auf dem Blatt `Data Connection` festgelegt ist. |
| `kustoDataFormat` |  Datenformat. Überschreibt das `Data format`, das auf dem Blatt `Data Connection` festgelegt ist. |
| `kustoIngestionMappingReference` |  Name der zu verwendenden vorhandenen Erfassungszuordnung. Überschreibt das `Column mapping`, das auf dem Blatt `Data Connection` festgelegt ist.|
| `kustoIgnoreFirstRecord` | Wenn `true` festgelegt wird, ignoriert Kusto die erste Zeile im Blob. Verwenden Sie diese Eigenschaft in Daten in einem Tabellenformat (CSV, TSV oder ähnliche), um die Header zu ignorieren. |
| `kustoExtentTags` | Zeichenfolgendarstellung von [Tags](kusto/management/extents-overview.md#extent-tagging), die an die resultierende Erweiterung angefügt werden. |
| `kustoCreationTime` |  Überschreibt [$IngestionTime](kusto/query/ingestiontimefunction.md?pivots=azuredataexplorer) für das Blob und ist als ISO 8601-Zeichenfolge formatiert. Verwenden Sie dies für einen Abgleich. |

> [!NOTE]
> Azure Data Explorer löscht die Blobs nach der Erfassung nicht.
> Behalten Sie die Blobs für drei bis fünf Tage bei.
> Informationen zum Löschen von Blobs finden Sie unter [Verwalten des Azure Blob Storage-Lebenszyklus](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal). 

## <a name="review-the-data-flow"></a>Überprüfen des Datenflusses

> [!NOTE]
> Azure Data Explorer verfügt über eine Aggregationsrichtlinie (Batching) für die Datenerfassung, die für die Optimierung des Erfassungsprozesses konzipiert ist.
Die Richtlinie ist standardmäßig auf fünf Minuten festgelegt.
Sie kann bei Bedarf zu einem späteren Zeitpunkt geändert werden. In diesem Artikel ist mit einer Wartezeit von wenigen Minuten zu rechnen.

1. Im Azure-Portal sehen Sie unter Ihrer Event Grid-Instanz den Anstieg der Aktivität während der App-Ausführung.

    ![Event Grid-Diagramm](media/ingest-data-event-grid/event-grid-graph.png)

1. Führen Sie in Ihrer Testdatenbank die folgende Abfrage aus, um zu überprüfen, wie viele Nachrichten bisher an die Datenbank gesendet wurden:

    ```kusto
    TestTable
    | count
    ```

1. Führen Sie in Ihrer Testdatenbank die folgende Abfrage aus, um den Inhalt der Nachrichten anzuzeigen:

    ```kusto
    TestTable
    ```

    Das Resultset sollte wie folgt aussehen.

    ![Nachrichten-Resultset](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie Ihre Event Grid-Instanz nicht mehr benötigen, bereinigen Sie **test-hub-rg**, um Kosten zu vermeiden.

1. Klicken Sie ganz links im Azure-Portal auf **Ressourcengruppen** und anschließend auf die erstellte Ressourcengruppe.  

    Wenn das linke Menü reduziert ist, wählen Sie ![Schaltfläche „Erweitern“](media/ingest-data-event-grid/expand.png) , um es zu erweitern.

   ![Auswählen einer zu löschenden Ressourcengruppe](media/ingest-data-event-grid/delete-resources-select.png)

1. Wählen Sie unter **test-resource-group** die Option **Ressourcengruppe löschen** aus.

1. Geben Sie im neuen Fenster den Namen der zu löschenden Ressourcengruppe (*test-hub-rg*) ein, und wählen Sie anschließend **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten in Azure Data Explorer](web-query-data.md)
