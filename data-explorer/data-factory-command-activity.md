---
title: Verwenden von Azure Data Explorer-Steuerungsbefehlen in Azure Data Factory
description: In diesem Thema verwenden Sie Azure Data Explorer-Steuerungsbefehle in Azure Data Factory.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: d59e77af1cebe9edb1c026a6dc557facec958917
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492958"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Verwenden der Azure Data Factory-Befehlsaktivität zum Ausführen von Azure Data Explorer-Steuerungsbefehlen

[Azure Data Factory](/azure/data-factory/) (ADF) ist ein cloudbasierter Datenintegrationsservice, der es Ihnen ermöglicht, eine Kombination von Aktivitäten mit den Daten durchzuführen. Verwenden Sie ADF, um datengesteuerte Workflows zur Orchestrierung und Automatisierung der Verschiebung und Transformation von Daten zu erstellen. Die Aktivität für **Azure Data Explorer-Befehle** in Azure Data Factory ermöglicht es Ihnen, [Azure Data Explorer-Steuerungsbefehle](kusto/concepts/index.md#control-commands) innerhalb eines ADF-Workflows auszuführen. In diesem Artikel erfahren Sie, wie Sie eine Pipeline mit einer Lookup- und einer ForEach-Aktivität erstellen, die eine Azure Data Explorer-Befehlsaktivität enthält.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* [Azure Data Explorer-Cluster und Datenbank](create-cluster-database-portal.md)
* Eine Datenquelle
* [Eine Data Factory](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Erstellen einer neuen Pipeline

1. Wählen Sie das Stiftwerkzeug **Verfassen** aus. 
1. Erstellen Sie eine neue Pipeline, indem Sie **+** und dann **Pipeline** aus dem Dropdownmenü auswählen.

   ![Erstellen einer neuen Pipeline](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Erstellen einer Lookup-Aktivität

Mithilfe einer [Lookup-Aktivität](/azure/data-factory/control-flow-lookup-activity) kann ein Dataset aus einer von Azure Data Factory unterstützten Datenquelle abgerufen werden. Die Ausgabe der Lookup-Aktivität kann in einer ForEach- oder anderen Aktivität verwendet werden.

1. Wählen Sie im Bereich **Aktivitäten** unter **Allgemein** die Aktivität **Lookup** aus. Ziehen Sie sie per Drag & Drop in den Hauptcanvas auf der rechten Seite.
 
    ![Auswählen der Lookup-Aktivität](media/data-factory-command-activity/select-activity.png)

1. Der Canvas enthält jetzt die von Ihnen erstellte Lookup-Aktivität. Verwenden Sie die Registerkarten unter dem Canvas, um alle relevanten Parameter zu ändern. Benennen Sie unter **Allgemein** die Aktivität um. 

    ![Bearbeiten der Lookup-Aktivität](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Klicken Sie auf den leeren Canvasbereich, um die Eigenschaften der Pipeline anzuzeigen. Verwenden Sie die Registerkarte **Allgemein**, um die Pipeline umzubenennen. Unsere Pipeline erhält den Namen *pipeline-4-docs*.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Erstellen eines Azure Data Explorer-Datasets in der Lookup-Aktivität

1. Wählen Sie unter **Einstellungen** Ihr vorab erstelltes Azure Data Explorer-**Quelldataset** oder **+ Neu** aus, um ein neues Dataset zu erstellen.
 
    ![Hinzufügen eines Datasets in den Lookup-Einstellungen](media/data-factory-command-activity/lookup-settings.png)

1. Wählen Sie das Dataset **Azur Data Explorer (Kusto)** im Fenster **Neues Dataset** aus. Wählen Sie **Weiter** aus, um das neue Dataset hinzuzufügen.

   ![Auswählen des neuen Datasets](media/data-factory-command-activity/select-new-dataset.png) 

1. Die neuen Azure Data Explorer-Datasetparameter werden unter **Einstellungen** angezeigt. Wählen Sie **Bearbeiten** aus, um die Parameter zu aktualisieren.

    ![Lookup-Einstellungen mit Azure Data Explorer-Dataset](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. Die neue Registerkarte **AzureDataExplorerTable** wird im Hauptcanvas geöffnet. 
    * Wählen Sie **Allgemein** aus, und bearbeiten Sie den Datasetnamen. 
    * Wählen Sie **Verbindung** aus, um die Eigenschaften des Datasets zu bearbeiten. 
    * Wählen Sie den **verknüpften Dienst** aus der Dropdownliste oder **+ Neu** aus, um einen neuen verknüpften Dienst zu erstellen.

    ![Bearbeiten von Azure Data Explorer-Dataseteigenschaften](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Beim Erstellen eines neuen verknüpften Diensts wird die Seite **Neuer verknüpfter Dienst (Azure Data Explorer)** geöffnet:

    ![ADX – Neuer verknüpfter Dienst](media/data-factory-command-activity/adx-new-linked-service.png)

   * Wählen Sie den **Namen** für den neuen verknüpften Azure Data Explorer-Dienst aus. Fügen Sie bei Bedarf eine **Beschreibung** hinzu.
   * Ändern Sie unter der Option zum **Verbinden über Integration Runtime** bei Bedarf die aktuellen Einstellungen. 
   * Wählen Sie unter **Kontoauswahlmethode** Ihren Cluster mit einer von zwei Methoden aus: 
        * Klicken Sie auf das Optionsfeld **From Azure subscription** (Aus Azure-Abonnement), und wählen Sie das Konto für Ihr **Azure-Abonnement** aus. Wählen Sie anschließend Ihren **Cluster** aus. In der Dropdownliste werden nur Cluster aufgeführt, die dem Benutzer gehören.
        * Sie können auch auf das Optionsfeld **Manuell eingeben** klicken und Ihren **Endpunkt** (Cluster-URL) eingeben.
    * Geben Sie den **Mandanten** an.
    * Geben Sie die **Dienstprinzipal-ID** ein. Die Prinzipal-ID muss je nach der Berechtigungsebene, die für den verwendeten Befehl erforderlich ist, über die entsprechenden Berechtigungen verfügen.
    * Klicken Sie auf die Schaltfläche **Dienstprinzipalschlüssel**, und geben Sie den **Dienstprinzipalschlüssel** ein.
    * Wählen Sie Ihre **Datenbank** im Dropdownmenü aus. Sie können auch das Kontrollkästchen **Bearbeiten** aktivieren und den Namen Ihrer Datenbank eingeben.
    * Wählen Sie **Verbindung testen** aus, um die erstellte Verbindung mit dem verknüpften Dienst zu testen. Wenn Sie eine Verbindung mit Ihrem Setup herstellen können, wird ein grünes Häkchen (**Verbindung erfolgreich**) angezeigt.
    * Wählen Sie **Fertig stellen** aus, um die Erstellung des verknüpften Diensts abzuschließen.

1. Nachdem Sie einen verknüpften Dienst eingerichtet haben, fügen Sie in **AzureDataExplorerTable** > **Verbindung** den **Tabellennamen** hinzu. Wählen Sie **Datenvorschau** aus, um sicherzustellen, dass die Daten ordnungsgemäß dargestellt werden.

   Ihr Dataset ist jetzt bereit, und Sie können die Bearbeitung Ihrer Pipeline fortsetzen.

### <a name="add-a-query-to-your-lookup-activity"></a>Hinzufügen einer Abfrage zu Ihrer Lookup-Aktivität

1. Fügen Sie in **pipeline-4-docs** > **Einstellungen** eine Abfrage im Textfeld **Abfrage** hinzu. Beispiel:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Ändern Sie bei Bedarf die Eigenschaften für **Abfragetimeout** oder **Keine Kürzung** und **Nur erste Zeile**. In diesem Flow behalten wir den Standardwert für **Abfragetimeout** bei und deaktivieren die Kontrollkästchen. 

    ![Endgültige Einstellungen der Lookup-Aktivität](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Erstellen einer For-Each-Aktivität 

Diese [ForEach](/azure/data-factory/control-flow-for-each-activity)-Aktivität wird verwendet, um eine Sammlung zu durchlaufen und die angegebenen Aktivitäten in einer Schleife auszuführen. 

1. Nun fügen Sie der Pipeline eine ForEach-Aktivität hinzu. Diese Aktivität verarbeitet die Daten, die von der Lookup-Aktivität zurückgegeben werden. 
    * Wählen Sie im Bereich **Aktivitäten** unter **Iteration & Bedingungen** die Aktivität **ForEach** aus, und ziehen Sie sie per Drag & Drop auf den Canvas.
    * Ziehen Sie eine Linie zwischen der Ausgabe der Lookup-Aktivität und der Eingabe der ForEach-Aktivität auf dem Canvas, um sie zu verbinden.

        ![ForEach-Aktivität](media/data-factory-command-activity/for-each-activity.png)

1.    Wählen Sie die ForEach-Aktivität auf dem Canvas aus. Auf der nachfolgenden Registerkarte **Einstellungen**:
    * Aktivieren Sie das Kontrollkästchen **Sequenziell** für eine sequenzielle Verarbeitung der Lookup-Ergebnisse, oder lassen Sie es deaktiviert, um eine Parallelverarbeitung zu erstellen.
    * Legen Sie **Batchanzahl** fest.
    * Stellen Sie in **Elemente** den folgenden Verweis auf den Ausgabewert bereit: *@activity('Lookup1').output.value*.

       ![Einstellungen für ForEach-Aktivitäten](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Erstellen einer Azure Data Explorer-Befehlsaktivität innerhalb der ForEach-Aktivität

1. Doppelklicken Sie auf die ForEach-Aktivität auf dem Canvas, um sie auf einem neuen Canvas zu öffnen und die Aktivitäten in „ForEach“ anzugeben.
1. Wählen Sie im Bereich **Aktivitäten** unter **Azure Data Explorer** die Aktivität **Azure Data Explorer-Befehl** aus, und ziehen Sie sie per Drag & Drop auf den Canvas.

    ![Azure Data Explorer-Befehlsaktivität](media/data-factory-command-activity/adx-command-activity.png)

1.    Wählen Sie auf der Registerkarte **Verbindung** denselben verknüpften Dienst aus, der zuvor erstellt wurde.

    ![Registerkarte „Verbindung“ der Azure Data Explorer-Befehlsaktivität](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Geben Sie auf der Registerkarte **Befehl** den folgenden Befehl ein:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    Der **Befehl** weist Azure Data Explorer an, die Ergebnisse einer bestimmten Abfrage in einen Blobspeicher in einem komprimierten Format zu exportieren. Die Ausführung erfolgt asynchron (mit dem asynchronen Modifizierer).
    Die Abfrage richtet sich an die Datenbankspalte der einzelnen Zeilen im Ergebnis der Lookup-Aktivität. Der **Befehlstimeout** kann unverändert beibehalten werden.

    ![Befehlsaktivität](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > Die Befehlsaktivität hat die folgenden Grenzwerte:
    > * Größenlimit: 1 MB Antwortgröße
    > * Zeitlimit: 20 Minuten (Standard), 1 Stunde (maximal).
    > * Bei Bedarf können Sie mit [AdminThenQuery](kusto/management/index.md#combining-queries-and-control-commands) eine Abfrage an das Ergebnis anfügen, um die resultierende Größe/Zeit zu verringern.

1.    Jetzt ist die Pipeline bereit. Sie können zur Hauptansicht der Pipeline zurückkehren, indem Sie auf den Namen der Pipeline klicken.

    ![Azure Data Explorer-Befehlspipeline](media/data-factory-command-activity/adx-command-pipeline.png)

1. Wählen Sie **Debuggen** aus, bevor Sie die Pipeline veröffentlichen. Der Fortschritt der Pipeline kann auf der Registerkarte **Ausgabe** überwacht werden.

    ![Ausgabe der Azure Data Explorer-Befehlsaktivität](media/data-factory-command-activity/command-activity-output.png)

1. Sie können **Alle veröffentlichen** und dann **Trigger hinzufügen** auswählen, um die Pipeline auszuführen. 

## <a name="control-command-outputs"></a>Ausgaben von Steuerungsbefehlen

Die Struktur der Ausgabe der Befehlsaktivität wird im Folgenden erläutert. Diese Ausgabe kann bei der nächsten Aktivität in der Pipeline verwendet werden.

### <a name="returned-value-of-a-non-async-control-command"></a>Rückgabewert eines nicht asynchronen Steuerungsbefehls

Bei einem nicht asynchronen Steuerungsbefehl ist die Struktur des zurückgegebenen Werts ähnlich der Struktur des Ergebnisses der Lookup-Aktivität. Das Feld `count` gibt die Anzahl der zurückgegebenen Datensätze an. Ein festes Arrayfeld `value` enthält eine Liste von Datensätzen. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>Rückgabewert eines asynchronen Steuerungsbefehls

Bei einem asynchronen Steuerungsbefehl fragt die Aktivität die Vorgangstabelle im Hintergrund ab, bis der asynchrone Vorgang abgeschlossen oder das Zeitlimit erreicht ist. Daher enthält der zurückgegebene Wert das Ergebnis von `.show operations OperationId` für die angegebene **OperationId**-Eigenschaft. Überprüfen Sie die Werte der Eigenschaften **State** und **Status**, um den erfolgreichen Abschluss des Vorgangs zu überprüfen.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Daten mithilfe von Azure Data Factory in Azure Data Explorer kopieren](data-factory-load-data.md).
* Erfahren Sie, wie Sie die [Azure Data Factory-Vorlage zum Massenkopieren aus der Datenbank in Azure Data Explorer](data-factory-template.md) verwenden.
