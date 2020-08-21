---
title: Erfassen von Daten aus IoT Hub in Azure Data Explorer
description: In diesem Artikel erfahren Sie, wie Sie Daten aus IoT Hub in Azure Data Explorer erfassen (laden).
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 36c724a001bb4438757316a456fbf85b55691c09
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201602"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>Erfassen von Daten aus IoT Hub in Azure Data Explorer 

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-Vorlage](data-connection-iot-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]

In diesem Artikel wird erläutert, wie Sie in Azure Data Explorer Daten aus IoT Hub (Big Data-Streamingplattform und IoT-Erfassungsdienst) erfassen.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Erstellen Sie [einen Testcluster und eine Testdatenbank](create-cluster-database-portal.md) mit dem Datenbanknamen *testdb*.
* [Eine Beispiel-App](https://github.com/Azure-Samples/azure-iot-samples-csharp) und eine Dokumentation zum Simulieren eines Geräts.
* [Visual Studio-2019](https://visualstudio.microsoft.com/vs/) zum Ausführen der Beispielapp

## <a name="create-an-iot-hub"></a>Erstellen einer IoT Hub-Instanz

[!INCLUDE [iot-hub-include-create-hub](includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Registrieren eines Geräts bei IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Erstellen einer Zieltabelle im Azure-Daten-Explorer

In diesem Abschnitt erstellen Sie in Azure Data Explorer eine Tabelle, an die IoT Hub Daten senden kann. Die Tabelle wird in dem Cluster und der Datenbank erstellt, die Sie unter [**Voraussetzungen**](#prerequisites) bereitgestellt haben.

1. Navigieren Sie im Azure-Portal zu Ihrem Cluster, und wählen Sie **Abfrage**.

    ![ADX-Abfrage im Portal](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Kopieren Sie den folgenden Befehl in das Fenster, und wählen Sie **Ausführen**, um die Tabelle (TestTable) zu erstellen, die die erfassten Daten erhalten soll.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Abfrage erstellen, „Ausführen“](media/ingest-data-iot-hub/run-create-query.png)

1. Kopieren Sie den folgenden Befehl in das Fenster, und wählen Sie **Ausführen**, um die eingehenden JSON-Daten den Spaltennamen und Datentypen der Tabelle (TestTable) zuzuordnen.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Herstellen einer Verbindung zwischen der Azure Data Explorer-Tabelle und IoT Hub

Als Nächstes stellen Sie über Azure Data Explorer eine Verbindung mit der IoT Hub-Instanz her. Sobald diese Verbindung hergestellt wurde, werden Daten, die bei IoT Hub eingehen, an die [erstellte Zieltabelle](#create-a-target-table-in-azure-data-explorer) gestreamt.

1. Wählen Sie auf der Symbolleiste die Option **Benachrichtigungen** aus, um zu überprüfen, ob die IoT Hub-Bereitstellung erfolgreich war.

1. Wählen Sie unter dem erstellten Cluster die Option **Datenbanken** und anschließend die erstellte Datenbank (**testdb**) aus.
    
    ![Testdatenbank auswählen](media/ingest-data-iot-hub/select-database.png)

1. Wählen Sie **Datenerfassung** und dann **Datenverbindung hinzufügen** aus. Füllen Sie dann das Formular mit den folgenden Informationen aus. Wählen Sie anschließend **Erstellen** aus.

    ![IoT Hub-Verbindung](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Datenquelle:**

    **Einstellung** | **Feldbeschreibung**
    |---|---|
    | Name der Datenverbindung | Der Name der Verbindung, die Sie in Azure Data Explorer erstellen möchten.
    | IoT Hub | IoT Hub-Name |
    | SAS-Richtlinie | Der Name der SAS-Richtlinie. Leseberechtigungen erforderlich |
    | Consumergruppe |  Die Consumergruppe, die im integrierten IoT Hub-Endpunkt definiert ist. |
    | Ereignissystemeigenschaften | Die [IoT Hub-Ereignissystemeigenschaften](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages). Beim Hinzufügen von Systemeigenschaften [erstellen](kusto/management/create-table-command.md) oder [aktualisieren](kusto/management/alter-table-command.md) Sie das Tabellenschema und die [Zuordnung](kusto/management/mappings.md), um die ausgewählten Eigenschaften einzubeziehen. | | | 

    > [!NOTE]
    > Im Falle eines [manuellen Failovers](/azure/iot-hub/iot-hub-ha-dr#manual-failover) muss die Datenverbindung neu erstellt werden.

    **Zieltabelle:**

    Es stehen zwei Routingoptionen für erfasste Daten zur Verfügung: *statisch* und *dynamisch*. 
    In diesem Artikel verwenden Sie statisches Routing, für das der Tabellenname, das Datenformat und die Zuordnung angegeben werden müssen. Lassen Sie das Kontrollkästchen **My data includes routing info** (Meine Daten enthalten Routinginformationen) daher deaktiviert.

     **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Tabelle | *TestTable* | Die Tabelle, die Sie in **testdb** erstellt haben. |
    | Datenformat | *JSON* | Die unterstützten Formate sind Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO und W3CLOG.|
    | Spaltenzuordnung | *TestMapping* | Die [Zuordnung](kusto/management/mappings.md), die Sie in **testdb** erstellt haben, um eingehende JSON-Daten den Spaltennamen und Datentypen von **testdb** zuzuordnen. Für „JSON“, „MULTILINE JSON“ und „AVRO“ erforderlich, für andere Formate optional.|
    | | |

    > [!NOTE]
    > * Wählen Sie **My data includes routing info** (Meine Daten enthalten Routinginformationen) aus, um dynamisches Routing zu verwenden. Dabei enthalten Ihre Daten die erforderlichen Routinginformationen, wie in den Kommentaren der [Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) zu sehen. Werden sowohl statische als auch dynamische Eigenschaften festgelegt, setzen die dynamischen Eigenschaften die statischen außer Kraft. 
    > * Nur Ereignisse, die nach dem Erstellen der Datenverbindung in die Warteschlange eingereiht werden, werden erfasst.

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>Generieren von Beispieldaten zu Testzwecken

Die Anwendung zur Simulation eines Geräts stellt eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrer IoT Hub-Instanz her und sendet simulierte Telemetriedaten für Temperatur und Luftfeuchtigkeit.

1. Laden Sie das C#-Beispielprojekt von https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip herunter, und extrahieren Sie das ZIP-Archiv.

1. Navigieren Sie in einem lokalen Terminalfenster zum Stammordner des C#-Beispielprojekts. Navigieren Sie anschließend zum Ordner **iot-hub\Quickstarts\simulated-device**.

1. Öffnen Sie die Datei **SimulatedDevice.cs** in einem Text-Editor Ihrer Wahl.

    Ersetzen Sie den Wert der Variablen `s_connectionString` durch die Geräteverbindungszeichenfolge aus [Registrieren eines Geräts bei IoT Hub](#register-a-device-to-the-iot-hub). Speichern Sie dann die Änderungen an der Datei **SimulatedDevice.cs**.

1. Führen Sie im lokalen Terminalfenster die folgenden Befehle aus, um die erforderlichen Pakete für die simulierte Geräteanwendung zu installieren:

    ```cmd/sh
    dotnet restore
    ```

1. Führen Sie im lokalen Terminalfenster den folgenden Befehl aus, um die simulierte Geräteanwendung zu erstellen und auszuführen:

    ```cmd/sh
    dotnet run
    ```

    Der folgende Screenshot zeigt die Ausgabe, während die Anwendung zur Simulation eines Geräts Telemetriedaten an Ihre IoT Hub-Instanz sendet:

    ![Ausführen des simulierten Geräts](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>Überprüfen des Datenflusses

Nachdem die App nun Daten generiert, sehen Sie den Datenfluss vom IoT-Hub zur Tabelle in Ihrem Cluster.

1. Im Azure-Portal sehen Sie unter Ihrem IoT-Hub den Anstieg der Aktivität während der App-Ausführung.

    ![IoT Hub-Metriken](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. Führen Sie in Ihrer Testdatenbank die folgende Abfrage aus, um zu überprüfen, wie viele Nachrichten bisher an die Datenbank gesendet wurden:

    ```Kusto
    TestTable
    | count
    ```

1. Führen Sie die folgende Abfrage aus, um den Inhalt der Nachrichten anzuzeigen:

    ```Kusto
    TestTable
    ```

    Das Resultset:
    
    ![Ergebnisse der erfassten Daten](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure Data Explorer verfügt über eine Aggregationsrichtlinie (Batching) für die Datenerfassung, die für die Optimierung des Erfassungsprozesses konzipiert ist. Da die Richtlinie standardmäßig mit fünf Minuten oder einem Datenvolumen von 500 MB konfiguriert ist, kann es zu Wartezeiten kommen. Aggregationsoptionen finden Sie unter [IngestionBatching policy](kusto/management/batchingpolicy.md) (IngestionBatching-Richtlinie). 
    > * Konfigurieren Sie Ihre Tabelle so, dass sie Streaming unterstützt und die Verzögerung bei der Antwortzeit entfernt wird. Weitere Informationen finden Sie unter [Streaming ingestion policy](kusto/management/streamingingestionpolicy.md) (Richtlinie für die Streamingerfassung). 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie nicht vorhaben, Ihren IoT Hub nochmals zu verwenden, sollten Sie die Ressourcengruppe bereinigen, damit Ihnen keine Kosten entstehen.

1. Klicken Sie ganz links im Azure-Portal auf **Ressourcengruppen** und anschließend auf die erstellte Ressourcengruppe.  

    Wenn das linke Menü reduziert ist, wählen Sie ![Schaltfläche „Erweitern“](media/ingest-data-event-hub/expand.png) , um es zu erweitern.

   ![Auswählen einer zu löschenden Ressourcengruppe](media/ingest-data-iot-hub/delete-resources-select.png)

1. Wählen Sie unter **test-resource-group** die Option **Ressourcengruppe löschen** aus.

2. Geben Sie im neuen Fenster den Namen der Ressourcengruppe ein, die Sie löschen möchten, und wählen Sie dann **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten in Azure Data Explorer](web-query-data.md)
