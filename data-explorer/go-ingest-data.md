---
title: Erfassen von Daten mit dem Go SDK für Azure Data Explorer
description: In diesem Artikel erfahren Sie, wie Sie Daten mit dem Go SDK in Azure Data Explorer erfassen (laden).
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/10/2020
ms.openlocfilehash: c133c3cf1185e7ffdb959ed6ea127af7502820c4
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342568"
---
# <a name="ingest-data-using-the-azure-data-explorer-go-sdk"></a>Erfassen von Daten mit dem Go SDK für Azure Data Explorer 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [Go](go-ingest-data.md)

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Er bietet eine [Go SDK-Clientbibliothek](kusto/api/golang/kusto-golang-client-library.md) für die Interaktion mit dem Azure Data Explorer-Dienst. Mit dem [Go SDK](https://github.com/Azure/azure-kusto-go) können Daten in Azure Data Explorer-Clustern erfasst, gesteuert und abgefragt werden. 

In diesem Artikel erstellen Sie zunächst eine Tabelle und eine Datenzuordnung in einem Testcluster. Anschließend reihen Sie die Erfassung für den Cluster mithilfe des Go SDK in die Warteschlange ein und überprüfen die Ergebnisse.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Installieren Sie [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Installieren Sie [Go](https://golang.org/) mit den folgenden [Mindestanforderungen für das Go SDK](kusto/api/golang/kusto-golang-client-library.md#minimum-requirements). 
* Erstellen Sie [einen Azure Data Explorer-Cluster und eine Datenbank](create-cluster-database-portal.md).
* Erstellen Sie eine [App-Registrierung, und gewähren Sie ihr Berechtigungen für die Datenbank](provision-azure-ad-app.md). Speichern Sie die Client-ID und den geheimen Clientschlüssel zur späteren Verwendung.

## <a name="install-the-go-sdk"></a>Installieren des Go SDK

Das Go SDK für Azure Data Explorer wird automatisch installiert, wenn Sie die Beispielanwendung ausführen, die [Go-Module](https://golang.org/ref/mod) verwendet. Wenn Sie das Go SDK für eine andere Anwendung installiert haben, erstellen Sie ein Go-Modul, und rufen Sie mithilfe von `go get` das Azure Data Explorer-Paket ab. Beispiel:

```console
go mod init foo.com/bar
go get github.com/Azure/azure-kusto-go/kusto
```

Die Paketabhängigkeit wird der Datei `go.mod` hinzugefügt. Verwenden Sie sie in Ihrer Go-Anwendung.

## <a name="review-the-code"></a>Überprüfen des Codes

Der Abschnitt [Überprüfen des Codes](#review-the-code) ist optional. Anhand der folgenden Codeausschnitte können Sie sich bei Interesse mit der Funktionsweise des Codes vertraut machen. Andernfalls können Sie mit dem Abschnitt [Ausführen der Anwendung](#run-the-application) fortfahren.

### <a name="authenticate"></a>Authenticate

Das Programm muss sich vor dem Ausführen von Vorgängen beim Azure Data Explorer-Dienst authentifizieren.

```go
auth := kusto.Authorization{Config: auth.NewClientCredentialsConfig(clientID, clientSecret, tenantID)}
client, err := kusto.New(kustoEndpoint, auth)
```

Hierzu wird eine Instanz von [kusto.Authorization](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Authorization) mit den Dienstprinzipal-Anmeldeinformationen erstellt. Anschließend wird damit unter Verwendung der Funktion [Neu](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#New]), die auch den Clusterendpunkt akzeptiert, ein Kusto-Client ([kusto.Client](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Client)) erstellt.

### <a name="create-table"></a>Tabelle erstellen

Der Befehl für die Tabellenerstellung wird durch eine [Kusto-Anweisung](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Stmt) dargestellt. Die Funktion [Mgmt](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Client.Mgmt) dient zum Ausführen von Verwaltungsbefehlen. Sie wird verwendet, um den Befehl für die Tabellenerstellung auszuführen. 

```go
func createTable(kc *kusto.Client, kustoDB string) {
    _, err := kc.Mgmt(context.Background(), kustoDB, kusto.NewStmt(createTableCommand))
    if err != nil {
        log.Fatal("failed to create table", err)
    }
    log.Printf("Table %s created in DB %s\n", kustoTable, kustoDB)
}
```

> [!TIP]
> Eine Kusto-Anweisung ist aus Sicherheitsgründen standardmäßig konstant. Von [`NewStmt`](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#NewStmt) werden Zeichenfolgenkonstanten akzeptiert. Die API [`UnsafeStmt`](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#UnsafeStmt) ermöglicht die Verwendung nicht konstanter Anweisungssegmente. Dies wird jedoch nicht empfohlen.

Der Kusto-Befehl für die Tabellenerstellung sieht wie folgt aus:

```kusto
.create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
```

### <a name="create-mapping"></a>Erstellen von Zuordnungen

Datenzuordnungen werden im Rahmen der Erfassung verwendet, um eingehende Daten den Spalten in Azure Data Explorer-Tabellen zuzuordnen. Weitere Informationen finden Sie unter [Datenzuordnungen](kusto/management/mappings.md). Die Zuordnung wird genau wie eine Tabelle mithilfe der Funktion `Mgmt` sowie mit dem Datenbanknamen und dem entsprechenden Befehl erstellt. Der vollständige Befehl steht im [GitHub-Repository für das Beispiel](https://github.com/Azure-Samples/Azure-Data-Explorer-Go-SDK-example-to-ingest-data/blob/main/main.go#L20) zur Verfügung.

```go
func createMapping(kc *kusto.Client, kustoDB string) {
    _, err := kc.Mgmt(context.Background(), kustoDB, kusto.NewStmt(createMappingCommand))
    if err != nil {
        log.Fatal("failed to create mapping - ", err)
    }
    log.Printf("Mapping %s created\n", kustoMappingRefName)
}
```

### <a name="ingest-data"></a>Erfassen von Daten

Eine Erfassung wird mithilfe einer Datei aus einem vorhandenen Azure Blob Storage-Container in eine Warteschlange eingereiht. 

```go
func ingestFile(kc *kusto.Client, blobStoreAccountName, blobStoreContainer, blobStoreToken, blobStoreFileName, kustoMappingRefName, kustoDB, kustoTable string) {
    kIngest, err := ingest.New(kc, kustoDB, kustoTable)
    if err != nil {
        log.Fatal("failed to create ingestion client", err)
    }
    blobStorePath := fmt.Sprintf(blobStorePathFormat, blobStoreAccountName, blobStoreContainer, blobStoreFileName, blobStoreToken)
    err = kIngest.FromFile(context.Background(), blobStorePath, ingest.FileFormat(ingest.CSV), ingest.IngestionMappingRef(kustoMappingRefName, ingest.CSV))

    if err != nil {
        log.Fatal("failed to ingest file", err)
    }
    log.Println("Ingested file from -", blobStorePath)
}
```

Der Client für die [Erfassung](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#Ingestion) wird mithilfe von [ingest.New](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#New) erstellt. Die Funktion [FromFile](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#Ingestion.FromFile) wird verwendet, um auf den Azure Blob Storage-URI zu verweisen. Der Name des Zuordnungsverweises und der Datentyp werden als [FileOption](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#FileOption) übergeben. 

## <a name="run-the-application"></a>Ausführen der Anwendung

1. Klonen Sie den Beispielcode von GitHub:

    ```console
    git clone https://github.com/Azure-Samples/Azure-Data-Explorer-Go-SDK-example-to-ingest-data.git
    cd Azure-Data-Explorer-Go-SDK-example-to-ingest-data
    ```

1. Führen Sie den Beispielcode aus, wie in diesem Ausschnitt aus `main.go` zu sehen: 

    ```go
    func main {
        ...
        dropTable(kc, kustoDB)
        createTable(kc, kustoDB)
        createMapping(kc, kustoDB)
        ingestFile(kc, blobStoreAccountName, blobStoreContainer, blobStoreToken, blobStoreFileName, kustoMappingRefName, kustoDB, kustoTable)
        ...
    }
    ```

    > [!TIP]
    > Sie können verschiedene Vorgangskombinationen ausprobieren, indem Sie die entsprechenden Funktionen in `main.go`auskommentieren bzw. kommentieren.

    Wenn Sie den Beispielcode ausführen, werden folgende Aktionen ausgeführt:
    
    1. **Tabellenlöschung:** Die Tabelle `StormEvents` wird gelöscht (sofern vorhanden).
    1. **Tabellenerstellung:** Die Tabelle `StormEvents` wird erstellt.
    1. **Zuordnungserstellung:** Die Zuordnung `StormEvents_CSV_Mapping` wird erstellt.
    1. **Dateierfassung:** Eine CSV-Datei (in Azure Blob Storage) wird zur Erfassung in eine Warteschlange eingereiht.

1. Erstellen Sie über die Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) einen Dienstprinzipal für die Authentifizierung. Legen Sie die Dienstprinzipalinformationen mit dem Clusterendpunkt und dem Datenbanknamen in Form von Umgebungsvariablen fest, die vom Programm verwendet werden:

    ```console
    export AZURE_SP_CLIENT_ID="<replace with appID>"
    export AZURE_SP_CLIENT_SECRET="<replace with password>"
    export AZURE_SP_TENANT_ID="<replace with tenant>"
    export KUSTO_ENDPOINT="https://<cluster name>.<azure region>.kusto.windows.net"
    export KUSTO_DB="name of the database"
    ```

1. Führen Sie das Programm aus:

    ```console
    go run main.go
    ```

    Die Ausgabe sollte in etwa wie folgt aussehen:

    ```console
    Connected to Azure Data Explorer
    Using database - testkustodb
    Failed to drop StormEvents table. Maybe it does not exist?
    Table StormEvents created in DB testkustodb
    Mapping StormEvents_CSV_Mapping created
    Ingested file from - https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D
    ```

## <a name="validate-and-troubleshoot"></a>Überprüfen und Behandeln von Problemen

Warten Sie fünf bis zehn Minuten, bis die in der Warteschlange befindliche Erfassung geplant wurde und die Daten in Azure Data Explorer geladen wurden. 

1. Melden Sie sich bei [https://dataexplorer.azure.com](https://dataexplorer.azure.com) an, und stellen Sie eine Verbindung mit Ihrem Cluster her. Führen Sie dann den folgenden Befehl aus, um die Anzahl von Datensätzen in der Tabelle `StormEvents` zu erhalten.

    ```kusto
    StormEvents | count
    ```

2. Führen Sie den folgenden Befehl in Ihrer Datenbank aus, um festzustellen, ob in den letzten vier Stunden Erfassungsfehler aufgetreten sind. Ersetzen Sie den Namen der Datenbank vor dem Ausführen.

    ```kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
    ```

1. Führen Sie den folgenden Befehl aus, um den Status aller Erfassungsvorgänge in den letzten vier Stunden anzuzeigen. Ersetzen Sie den Namen der Datenbank vor dem Ausführen.

    ```kusto
    .show operations
    | where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
    | summarize arg_max(LastUpdatedOn, *) by OperationId
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie unsere anderen Artikel durcharbeiten möchten, behalten Sie die erstellten Ressourcen bei. Führen Sie andernfalls den folgenden Befehl in Ihrer Datenbank aus, um die Tabelle `StormEvents` zu löschen:

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Nächste Schritte

[Write queries](write-queries.md) (Schreiben von Abfragen)