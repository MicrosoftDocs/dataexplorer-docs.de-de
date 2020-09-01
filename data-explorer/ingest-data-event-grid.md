---
title: Erfassen von Azure-Blobs in Azure Data Explorer
description: In diesem Artikel erfahren Sie, wie Sie Speicherkontodaten unter Verwendung eines Event Grid-Abonnements an Azure Data Explorer senden.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 25c82c8890342e00279d137eb749f3acc7df986f
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874986"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Erfassen von Blobs in Azure Data Explorer durch das Abonnieren von Event Grid-Benachrichtigungen

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-Vorlage](data-connection-event-grid-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]

In diesem Artikel erfahren Sie, wie Blobs aus Ihrem Speicherkonto unter Verwendung einer Event Grid-Datenverbindung in Azure Data Explorer erfasst werden. Sie erstellen eine Event Grid Datenverbindung und legen ein [Azure Event Grid](/azure/event-grid/overview)-Abonnement fest. Das Event Grid-Abonnement leitet Ereignisse aus Ihrem Speicherkonto über einen Azure Event Hub an Azure Data Explorer weiter. Anschließend wird ein Beispiel für den Datenfluss im gesamten System gezeigt.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Erstellen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/).
* [Ein Cluster und eine Datenbank](create-cluster-database-portal.md).
* [Ein Speicherkonto](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

## <a name="create-a-target-table-in-azure-data-explorer"></a>Erstellen einer Zieltabelle im Azure-Daten-Explorer

Erstellen Sie in Azure Data Explorer eine Tabelle, an die Event Hubs Daten senden kann. Erstellen Sie die Tabelle in dem Cluster und der Datenbank, den bzw. die Sie unter „Voraussetzungen“ vorbereitet haben.

1. Wählen Sie im Azure-Portal unter Ihrem Cluster die Option **Abfrage** aus.

    :::image type="content" source="media/ingest-data-event-grid/query-explorer-link.png" alt-text="Link zum Abfrage-Explorer":::    

1. Kopieren Sie den folgenden Befehl in das Fenster, und wählen Sie **Ausführen**, um die Tabelle (TestTable) zu erstellen, die die erfassten Daten erhalten soll.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    :::image type="content" source="media/ingest-data-event-grid/run-create-table.png" alt-text="Ausführen des Befehls „create table“":::

1. Kopieren Sie den folgenden Befehl in das Fenster, und wählen Sie **Ausführen**, um die eingehenden JSON-Daten den Spaltennamen und Datentypen der Tabelle (TestTable) zuzuordnen.

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Erstellen einer Event Grid-Datenverbindung in Azure Data Explorer

Verbinden Sie nun das Speicherkonto mit Azure Data Explorer, so dass die in den Speicher fließenden Daten in die Testtabelle gestreamt werden. 

1. Wählen Sie unter dem von Ihnen erstellten Cluster **Datenbanken** > **TestDatabase**.

    :::image type="content" source="media/ingest-data-event-grid/select-test-database.png" alt-text="Auswählen der Testdatenbank":::

1. Wählen Sie **Datenerfassung** > **Datenverbindung hinzufügen**.

    :::image type="content" source="media/ingest-data-event-grid/data-ingestion-create.png" alt-text="Hinzufügen der Datenverbindung für die Datenerfassung":::

1. Wählen Sie den Verbindungstyp aus: **Blobspeicher**.

1. Füllen Sie das Formular mit den folgenden Informationen aus:

    :::image type="content" source="media/ingest-data-event-grid/create-event-grid-data-connection-basics.png" alt-text="Ausfüllen des Event Grid-Formulars mit grundlegenden Verbindungsinformationen":::

     Datenquelle:

    |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
    |---|---|---|
    | Name der Datenverbindung | *test-grid-connection* | Der Name der Verbindung, die Sie im Azure Data Explorer erstellen möchten.|
    | Speicherkontoabonnement | Ihre Abonnement-ID | Die Abonnement-ID, unter der sich Ihr Speicherkonto befindet.|
    | Speicherkonto | *gridteststorage1* | Der Name des zuvor erstellten Speicherkontos.|
    | Ressourcenerstellung | *Automatisch* | Legen Sie fest, ob Azure Data Explorer ein Event Grid Abonnement, einen Event Hub-Namespace und einen Event Hub für Sie erstellen soll. Eine ausführliche Erläuterung der manuellen Erstellung Event Grid-Abonnements finden Sie unter den Verweisen im Abschnitt [Erstellen eines Event Grid-Abonnements in Ihrem Speicherkonto](ingest-data-event-grid.md).|

1. Wählen Sie **Filtereinstellungen** aus, wenn Sie bestimmte Themen nachverfolgen möchten. Legen Sie die Filter für die Benachrichtigungen wie folgt fest:
    * Das Feld **Präfix** ist das *Literalpräfix* des Themas. Da das angewendete Muster *startswith* ist, kann es mehrere Container, Ordner oder Blobs umfassen. Platzhalter sind nicht zulässig.
        * Zum Definieren eines Filters für den Blobcontainer *muss* das Feld wie folgt festgelegt werden: *`/blobServices/default/containers/[container prefix]`* .
        * Zum Definieren eines Filters für ein Blobpräfix (oder einen Ordner in Azure Data Lake Gen2) *muss* das Feld wie folgt festgelegt werden: *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
    * Das Feld **Suffix** ist das *Literalsuffix* des Blobs. Platzhalter sind nicht zulässig.
    * Das Feld **Groß-/Kleinschreibung beachten** Feld gibt an, ob bei den Präfix- und Suffixfilter nach Groß-/Kleinschreibung unterschieden wird.
    * Weitere Informationen zum Filtern von Ereignissen finden Sie unter [Blob Storage-Ereignisse](/azure/storage/blobs/storage-blob-event-overview#filtering-events).
    
    :::image type="content" source="media/ingest-data-event-grid/filter-settings.png" alt-text="Event Grid-Filtereinstellungen":::    

1. Klicken Sie auf **Weiter: Erfassungseigenschaften**.

1. Füllen Sie das Formular mit den folgenden Informationen aus, und wählen Sie **Weiter: Überprüfen + erstellen**. Für Tabellen- und Zuordnungsnamen wird die Groß-/Kleinschreibung beachtet:

   :::image type="content" source="media/ingest-data-event-grid/create-event-grid-data-connection-ingest-properties.png" alt-text="Überprüfen und Erstellen von Erfassungseigenschaften für Tabellen und Zuordnungen":::

    Erfassungseigenschaften:

     **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Tabelle | *TestTable* | Die Tabelle, die Sie unter **TestDatabase** erstellt haben. |
    | Datenformat | *JSON* | Die unterstützten Formate sind Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO, RAW und W3CLOG. Die unterstützten Komprimierungsoptionen sind Zip und GZip. |
    | Zuordnung | *TestMapping* | Die Zuordnung, die Sie in **TestDatabase** erstellt haben, um eingehende JSON-Daten den Spaltennamen und Datentypen von **TestTable** zuzuordnen.|

1. Überprüfen Sie die Ressourcen, die automatisch für Sie erstellt wurden, und wählen Sie **Erstellen** aus.

    :::image type="content" source="media/ingest-data-event-grid/create-event-grid-data-connection-review-create.png" alt-text="Überprüfen und Erstellen einer Datenverbindung für Event Grid":::

1. Warten Sie, bis die Bereitstellung abgeschlossen wurde. Wenn bei Ihrer Bereitstellung ein Fehler aufgetreten ist, können Sie neben der fehlerhaften Phase auf **Vorgangsdetails** klicken, um weitere Informationen zur Fehlerursache anzuzeigen. Sie können auch **Erneut bereitstellen** auswählen, um die Bereitstellung der Ressourcen zu wiederholen.

    :::image type="content" source="media/ingest-data-event-grid/deploy-event-grid-resources.png" alt-text="Bereitstellen von Event Grid-Ressourcen":::

## <a name="generate-sample-data"></a>Generieren von Beispieldaten

Nachdem nun eine Verbindung zwischen Azure Data Explorer und dem Speicherkonto besteht, können Sie Beispieldaten erstellen und in den Speichercontainer hochladen.

Wir verwenden ein kleines Shellskript, das einige grundlegenden Azure CLI-Befehle für die Interaktion mit Azure Storage-Ressourcen aufruft. Das Skript bewirkt Folgendes: 
1. Erstellen eines Blobcontainers in Ihrem Speicherkonto
1. Hochladen einer vorhandenen Datei (als Blob) in diesen Container
1. Auflisten der Blobs im Container 

Das Skript kann per [Azure Cloud Shell](/azure/cloud-shell/overview) direkt im Portal ausgeführt werden.

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

[!INCLUDE [ingestion-properties-event-grid](includes/ingestion-properties-event-grid.md)]

> [!NOTE]
> Azure Data Explorer löscht die Blobs nach der Erfassung nicht.
> Behalten Sie die Blobs für drei bis fünf Tage bei.
> Informationen zum Löschen von Blobs finden Sie unter [Verwalten des Azure Blob Storage-Lebenszyklus](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal). 

## <a name="review-the-data-flow"></a>Überprüfen des Datenflusses

> [!NOTE]
> Azure Data Explorer verfügt über eine Aggregationsrichtlinie (Batching) für die Datenerfassung, die für die Optimierung des Erfassungsprozesses konzipiert ist.
Die Richtlinie ist standardmäßig auf fünf Minuten festgelegt.
Sie kann bei Bedarf zu einem späteren Zeitpunkt geändert werden. In diesem Artikel ist mit einer Wartezeit von wenigen Minuten zu rechnen.

1. Im Azure-Portal sehen Sie unter Ihrer Event Grid-Instanz den Anstieg der Aktivität während der App-Ausführung.

    :::image type="content" source="media/ingest-data-event-grid/event-grid-graph.png" alt-text="Aktivitätsgraph für Event Grid":::

1. Führen Sie in Ihrer Testdatenbank die folgende Abfrage aus, um zu überprüfen, wie viele Nachrichten bisher an die Datenbank gesendet wurden:

    ```kusto
    TestTable
    | count
    ```

1. Führen Sie in Ihrer Testdatenbank die folgende Abfrage aus, um den Inhalt der Nachrichten anzuzeigen:

    ```kusto
    TestTable
    ```

    Das Resultset sollte wie auf der folgenden Abbildung aussehen:

    :::image type="content" source="media/ingest-data-event-grid/table-result.png" alt-text="Nachrichten-Resultset für Event Grid":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie Ihre Event Grid-Instanz nicht erneut verwenden möchten, bereinigen Sie das Event Grid-Abonnement, den Event Hub-Namespace und den Event Hub, die automatisch für Sie erstellt wurden, um Kosten zu vermeiden.

1. Wählen Sie im Azure-Portal im Menü auf der linken Seite die Option **Alle Ressourcen** aus.

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-all-resource.png" alt-text="Auswählen aller Ressourcen für die Event Grid-Bereinigung":::    

1. Suchen Sie nach Ihrem Event Hub-Namespace, und wählen Sie **Löschen** aus, um ihn zu löschen:

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-find-eventhub-namespace-delete.png" alt-text="Bereinigen des Event Hub-Namespace":::

1. Bestätigen Sie im Formular „Ressourcen löschen“ den Löschvorgang, um den Event Hub-Namespace und die Event Hub-Ressourcen zu löschen.

1. Wechseln Sie zum Speicherkonto. Wählen Sie im linken Menü die Option **Ereignisse** aus:

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-events.png" alt-text="Auswählen von Ereignissen zur Bereinigung für Event Grid":::

1. Wählen Sie unterhalb des Graphs Ihr Event Grid-Abonnement aus, und wählen Sie dann **Löschen** aus, um es zu löschen:

    :::image type="content" source="media/ingest-data-event-grid/delete-event-grid-subscription.png" alt-text="Löschen des Event Grid-Abonnements":::

1. Wenn Sie Ihre Event Grid Datenverbindung löschen möchten, wechseln Sie zu Ihrem Azure Data Explorer-Cluster. Wählen Sie im Menü auf der linken Seite **Datenbanken** aus.

1. Wählen Sie Ihre Datenbank **TestDatabase** aus:

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-database.png" alt-text="Auswählen einer Datenbank zum Bereinigen von Ressourcen":::

1. Wählen Sie im Menü auf der linken Seite die Option **Datenerfassung** aus.

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-data-ingestion.png" alt-text="Auswählen von „Datenerfassung“ zum Bereinigen von Ressourcen":::

1. Wählen Sie Ihre Datenverbindung *test-grid-connection* aus, und wählen Sie dann **Löschen** aus, um sie zu löschen.

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten in Azure Data Explorer](web-query-data.md)
