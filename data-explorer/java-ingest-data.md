---
title: Erfassen von Daten mit dem Java SDK für Azure Data Explorer
description: In diesem Artikel erfahren Sie, wie Sie Daten mithilfe des Java SDK in Azure Data Explorer erfassen (laden).
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 45b0ce7b1716a4ed2ab7277b6c99a5d95f8ad5af
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906205"
---
# <a name="ingest-data-using-the-azure-data-explorer-java-sdk"></a>Erfassen von Daten mithilfe des Java SDK für Azure Data Explorer 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [Go](go-ingest-data.md)
> * [Java](java-ingest-data.md)

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Mithilfe der [Java-Clientbibliothek](kusto/api/java/kusto-java-client-library.md) können Sie in Azure Data Explorer-Clustern Daten erfassen, Steuerungsbefehle ausgeben und Daten abfragen.

In diesem Artikel erfahren Sie, wie Sie Daten mithilfe der Azure Data Explorer-Java-Bibliothek erfassen. Sie erstellen zunächst eine Tabelle und eine Datenzuordnung in einem Testcluster. Anschließend reihen Sie eine Erfassung aus Blobspeicher für den Cluster mithilfe des Java SDK in die Warteschlange ein und überprüfen die Ergebnisse.

## <a name="prerequisites"></a>Voraussetzungen

* [Kostenloses Azure-Konto](https://azure.microsoft.com/free/)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Mindestens JDK-Version 1.8
* [Maven](https://maven.apache.org/download.cgi).
* [Ein Cluster und eine Datenbank.](create-cluster-database-portal.md)
* Erstellen Sie eine [App-Registrierung, und gewähren Sie ihr Berechtigungen für die Datenbank](provision-azure-ad-app.md). Speichern Sie die Client-ID und den geheimen Clientschlüssel zur späteren Verwendung.

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Abschnitt ist optional. Anhand der folgenden Codeausschnitte können Sie sich mit der Funktionsweise des Codes vertraut machen. Wenn Sie Abschnitt überspringen möchten, gehen Sie zu [Ausführen der Anwendung](#run-the-application).

### <a name="authentication"></a>Authentifizierung

Das Programm verwendet Anmeldeinformationen für die Azure Active Directory-Authentifizierung mit ConnectionStringBuilder.

1. Erstellen Sie `com.microsoft.azure.kusto.data.Client` für Abfrage und Verwaltung.

    ```java
    static Client getClient() throws Exception {
        ConnectionStringBuilder csb = ConnectionStringBuilder.createWithAadApplicationCredentials(endpoint, clientID, clientSecret, tenantID);
        return ClientFactory.createClient(csb);
    }
    ```

1. Erstellen und verwenden Sie ein Element vom Typ `com.microsoft.azure.kusto.ingest.IngestClient`, um die Datenerfassung in Azure Data Explorer in eine Warteschlange einzureihen:

    ```java
    static IngestClient getIngestionClient() throws Exception {
        String ingestionEndpoint = "https://ingest-" + URI.create(endpoint).getHost();
        ConnectionStringBuilder csb = ConnectionStringBuilder.createWithAadApplicationCredentials(ingestionEndpoint, clientID, clientSecret);
        return IngestClientFactory.createClient(csb);
    }
    ```

### <a name="management-commands"></a>Befehle für Verwaltung

[Steuerungsbefehle](kusto/management/commands.md) (etwa [`.drop`](kusto/management/drop-function.md) und [`.create`](kusto/management/create-function.md)) werden durch Aufrufen von `execute` für ein `com.microsoft.azure.kusto.data.Client`-Objekt ausgeführt.

Die Tabelle `StormEvents` wird beispielsweise wie folgt erstellt:

```java
static final String createTableCommand = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)";

static void createTable(String database) {
    try {
        getClient().execute(database, createTableCommand);
        System.out.println("Table created");
    } catch (Exception e) {
        System.out.println("Failed to create table: " + e.getMessage());
        return;
    }
    
}
```

### <a name="data-ingestion"></a>Datenerfassung

Reihen Sie eine Erfassung mithilfe einer Datei aus einem vorhandenen Azure Blob Storage-Container in eine Warteschlange ein. 
* Verwenden Sie `BlobSourceInfo` zum Angeben des Blob Storage-Pfads.
* Verwenden Sie `IngestionProperties` zum Definieren der Tabelle, der Datenbank, des Zuordnungsnamens und des Datentyps. Im folgenden Beispiel lautet der Datentyp `CSV`.

```java
    ...
    static final String blobPathFormat = "https://%s.blob.core.windows.net/%s/%s%s";
    static final String blobStorageAccountName = "kustosamplefiles";
    static final String blobStorageContainer = "samplefiles";
    static final String fileName = "StormEvents.csv";
    static final String blobStorageToken = "??st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    ....

    static void ingestFile(String database) throws InterruptedException {
        String blobPath = String.format(blobPathFormat, blobStorageAccountName, blobStorageContainer,
                fileName, blobStorageToken);
        BlobSourceInfo blobSourceInfo = new BlobSourceInfo(blobPath);

        IngestionProperties ingestionProperties = new IngestionProperties(database, tableName);
        ingestionProperties.setDataFormat(DATA_FORMAT.csv);
        ingestionProperties.setIngestionMapping(ingestionMappingRefName, IngestionMappingKind.Csv);
        ingestionProperties.setReportLevel(IngestionReportLevel.FailuresAndSuccesses);
        ingestionProperties.setReportMethod(IngestionReportMethod.QueueAndTable);
    ....
```

Der Erfassungsprozess startet in einem separaten Thread, und der `main`-Thread wartet auf den Abschluss des Erfassungsthreads. Bei diesem Prozess wird [CountdownLatch](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CountDownLatch.html) verwendet. Die Erfassungs-API (`IngestClient#ingestFromBlob`) ist nicht asynchron. Mit einer `while`-Schleife wird alle fünf Sekunden der aktuelle Status abgefragt, und es wird gewartet, bis der Erfassungsstatus `Pending` in einen anderen Status geändert wird. Der endgültige Status kann `Succeeded`, `Failed` oder `PartiallySucceeded` lauten.

```java
        ....
        CountDownLatch ingestionLatch = new CountDownLatch(1);
        new Thread(new Runnable() {
            @Override
            public void run() {
                IngestionResult result = null;
                try {
                    result = getIngestionClient().ingestFromBlob(blobSourceInfo, ingestionProperties);
                } catch (Exception e) {
                    ingestionLatch.countDown();
                }
                try {
                    IngestionStatus status = result.getIngestionStatusCollection().get(0);
                    while (status.status == OperationStatus.Pending) {
                        Thread.sleep(5000);
                        status = result.getIngestionStatusCollection().get(0);
                    }
                    ingestionLatch.countDown();
                } catch (Exception e) {
                    ingestionLatch.countDown();
                }
            }
        }).start();
        ingestionLatch.await();
    }
```

> [!TIP]
> Es gibt noch weitere Methoden, um die Erfassung für verschiedene Anwendungen asynchron zu verarbeiten. Beispielsweise können Sie [`CompletableFuture`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) verwenden, um eine Pipeline zu erstellen, die die Aktion nach der Erfassung definiert, etwa das Abfragen der Tabelle oder das Behandeln von Ausnahmen, die an `IngestionStatus` gemeldet wurden.

## <a name="run-the-application"></a>Ausführen der Anwendung

### <a name="general"></a>Allgemein

Wenn Sie den Beispielcode ausführen, werden folgende Aktionen ausgeführt:

   1. **Tabellenlöschung:** Die Tabelle `StormEvents` wird gelöscht (sofern vorhanden).
   1. **Tabellenerstellung:** Die Tabelle `StormEvents` wird erstellt.
   1. **Zuordnungserstellung:** Die Zuordnung `StormEvents_CSV_Mapping` wird erstellt.
   1. **Dateierfassung:** Eine CSV-Datei (in Azure Blob Storage) wird zur Erfassung in eine Warteschlange eingereiht.

Der folgende Beispielcode stammt aus `App.java`:

```java
public static void main(final String[] args) throws Exception {
    dropTable(database);
    createTable(database);
    createMapping(database);
    ingestFile(database);
}
```

> [!TIP]
> Sie können verschiedene Vorgangskombinationen ausprobieren, indem Sie die entsprechenden Methoden in `App.java` auskommentieren bzw. kommentieren.

### <a name="run-the-application"></a>Ausführen der Anwendung

1. Klonen Sie den Beispielcode von GitHub:

    ```console
    git clone https://github.com/Azure-Samples/azure-data-explorer-java-sdk-ingest.git
    cd azure-data-explorer-java-sdk-ingest
    ```

1. Legen Sie die Dienstprinzipalinformationen mit den folgenden Informationen als Umgebungsvariablen fest, die vom Programm verwendet werden:
    * Clusterendpunkt 
    * Datenbankname 

    ```console
    export AZURE_SP_CLIENT_ID="<replace with appID>"
    export AZURE_SP_CLIENT_SECRET="<replace with password>"
    export KUSTO_ENDPOINT="https://<cluster name>.<azure region>.kusto.windows.net"
    export KUSTO_DB="name of the database"
    ```

1. Erstellen und führen Sie Folgendes aus:

    ```console
    mvn clean package
    java -jar target/adx-java-ingest-jar-with-dependencies.jar
    ```

    Die Ausgabe ähnelt der folgenden:

    ```console
    Table dropped
    Table created
    Mapping created
    Waiting for ingestion to complete...
    ```

Warten Sie einige Minuten, bis die Erfassung abgeschlossen ist. Bei erfolgreicher Ausführung wird die folgende Protokollmeldung angezeigt: `Ingestion completed successfully`. Sie können das Programm an dieser Stelle beenden und mit dem nächsten Schritt fortfahren, ohne den Erfassungsprozess zu beeinträchtigen, der bereits in die Warteschlange eingereiht wurde.

## <a name="validate"></a>Überprüfen

Warten Sie fünf bis zehn Minuten, bis die in der Warteschlange befindliche Erfassung geplant wurde und die Daten in Azure Data Explorer geladen wurden. 

1. Melden Sie sich bei [https://dataexplorer.azure.com](https://dataexplorer.azure.com) an, und stellen Sie eine Verbindung mit Ihrem Cluster her. 
1. Führen Sie den folgenden Befehl aus, um die Anzahl von Datensätzen in der Tabelle `StormEvents` zu erhalten:
    
    ```kusto
    StormEvents | count
    ```

## <a name="troubleshoot"></a>Problembehandlung

1. Führen Sie den folgenden Befehl in Ihrer Datenbank aus, um festzustellen, ob in den letzten vier Stunden Erfassungsfehler aufgetreten sind: 

    ```kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
    ```

1. Führen Sie den folgenden Befehl aus, um den Status aller Erfassungsvorgänge in den letzten vier Stunden anzuzeigen:

    ```kusto
    .show operations
    | where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
    | summarize arg_max(LastUpdatedOn, *) by OperationId
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die soeben erstellten Ressourcen nicht verwenden möchten, führen Sie den folgenden Befehl in der Datenbank aus, um die Tabelle `StormEvents` zu löschen:

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Nächste Schritte

[Write queries](write-queries.md) (Schreiben von Abfragen)
