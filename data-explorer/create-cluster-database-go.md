---
title: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mit dem Azure Go SDK
description: Hier erfahren Sie, wie Sie mit dem Azure Go SDK einen Azure Data Explorer-Cluster und eine Datenbank erstellen, auflisten und löschen.
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 4eedb1a7f9204ff211d258938666cc6ce3b3391a
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906194"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-go"></a>Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mithilfe von Go

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Go](create-cluster-database-go.md)
> * [ARM-Vorlage](create-cluster-database-resource-manager.md)

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. Um den Azure Data Explorer zu verwenden, erstellen Sie zuerst einen Cluster und anschließend eine oder mehrere Datenbanken in diesem Cluster. Anschließend erfassen (laden) Sie Daten in eine Datenbank, damit Sie diese abfragen können. 

In diesem Artikel erstellen Sie mithilfe von [Go](https://golang.org/) einen Azure Data Explorer-Cluster und eine Datenbank. Anschließend können Sie den neuen Cluster und die Datenbank auflisten und löschen sowie Vorgänge für Ihre Ressourcen ausführen.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.
* Installieren Sie [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Installieren Sie eine geeignete Version von Go. Weitere Informationen zu unterstützten Releases finden Sie im [Azure Go SDK](https://github.com/Azure/azure-sdk-for-go).

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Abschnitt ist optional. Anhand der folgenden Codeausschnitte können Sie sich bei Interesse mit der Funktionsweise des Codes vertraut machen. Andernfalls können Sie mit dem Abschnitt [Ausführen der Anwendung](#run-the-application) fortfahren.

### <a name="authentication"></a>Authentifizierung

Das Programm muss sich vor dem Ausführen von Vorgängen bei Azure Data Explorer authentifizieren. Der [Authentifizierungstyp „Clientanmeldeinformationen“](https://docs.microsoft.com/azure/developer/go/azure-sdk-authorization#use-environment-based-authentication) wird von [auth.NewAuthorizerFromEnvironment](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromEnvironment) verwendet. Dabei wird nach den folgenden vordefinierten Umgebungsvariablen gesucht: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`, `AZURE_TENANT_ID`.

Im folgenden Beispiel wird gezeigt, wie mithilfe dieser Vorgehensweise ein Element vom Typ [kusto.ClustersClient](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#ClustersClient) erstellt wird:

```go
func getClustersClient(subscription string) kusto.ClustersClient {
    client := kusto.NewClustersClient(subscription)
    authR, err := auth.NewAuthorizerFromEnvironment()
    if err != nil {
        log.Fatal(err)
    }
    client.Authorizer = authR

    return client
}
```

> [!TIP]
> Verwenden Sie für die lokale Entwicklung die Funktion [auth.NewAuthorizerFromCLIWithResource](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromCLIWithResource), wenn Sie die Azure CLI installiert und für die Authentifizierung konfiguriert haben.

### <a name="create-cluster"></a>Cluster erstellen

Verwenden Sie die Funktion [CreateOrUpdate](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#ClustersClient.CreateOrUpdate) für `kusto.ClustersClient`, um einen neuen Azure Data Explorer-Cluster zu erstellen. Warten Sie, bis der Prozess abgeschlossen ist, bevor Sie die Ergebnisse überprüfen.

```go
func createCluster(sub, name, location, rgName string) {
    ...
    result, err := client.CreateOrUpdate(ctx, rgName, name, kusto.Cluster{Location: &location, Sku: &kusto.AzureSku{Name: kusto.DevNoSLAStandardD11V2, Capacity: &numInstances, Tier: kusto.Basic}})
    ...
    err = result.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := result.Result(client)
}
```

### <a name="list-clusters"></a>Auflisten der Cluster

Verwenden Sie die Funktion [ListByResourceGroup](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#ClustersClient.ListByResourceGroup) für `kusto.ClustersClient`, um ein Element vom Typ [kusto.ClusterListResult](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#ClusterListResult) abzurufen, das anschließend durchlaufen wird, um die Ausgabe im Tabellenformat anzuzeigen.


```go
func listClusters(sub, rgName string) {
    ...
    result, err := getClustersClient(sub).ListByResourceGroup(ctx, rgName)
    ...
    for _, c := range *result.Value {
        // setup tabular representation
    }
    ...
}
```

### <a name="create-database"></a>Erstellen einer Datenbank

Verwenden Sie die Funktion [CreateOrUpdate](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#DatabasesClient.CreateOrUpdate) für [kusto.DatabasesClient](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#DatabasesClient), um eine neue Azure Data Explorer-Datenbank in einem vorhandenen Cluster zu erstellen. Warten Sie, bis der Prozess abgeschlossen ist, bevor Sie die Ergebnisse überprüfen.


```go
func createDatabase(sub, rgName, clusterName, location, dbName string) {
    future, err := client.CreateOrUpdate(ctx, rgName, clusterName, dbName, kusto.ReadWriteDatabase{Kind: kusto.KindReadWrite, Location: &location})
    ...
    err = future.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := future.Result(client)
    ...
}
```

### <a name="list-databases"></a>Auflisten von Datenbanken

Verwenden Sie die Funktion [ListByCluster](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#DatabasesClient.ListByCluster) für `kusto.DatabasesClient`, um ein Element vom Typ [kusto.DatabaseListResult](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#DatabaseListResult) abzurufen, das anschließend durchlaufen wird, um die Ausgabe im Tabellenformat anzuzeigen.


```go
func listDatabases(sub, rgName, clusterName string) {
    result, err := getDBClient(sub).ListByCluster(ctx, rgName, clusterName)
    ...
    for _, db := range *result.Value {
        // setup tabular representation
    }
    ...
}
```

### <a name="delete-database"></a>Datenbank löschen

Verwenden Sie die Funktion [Delete](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#DatabasesClient.Delete) für `kusto.DatabasesClient`, um eine vorhandene Datenbank in einem Cluster zu löschen. Warten Sie, bis der Prozess abgeschlossen ist, bevor Sie die Ergebnisse überprüfen.

```go
func deleteDatabase(sub, rgName, clusterName, dbName string) {
    ...
    future, err := getDBClient(sub).Delete(ctx, rgName, clusterName, dbName)
    ...
    err = future.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := future.Result(client)
    if r.StatusCode == 200 {
        // determine success or failure
    }
    ...
}
```

### <a name="delete-cluster"></a>Löschen von Clustern

Verwenden Sie die Funktion [Delete](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#ClustersClient.Delete) für `kusto.ClustersClient`, um einen Cluster zu löschen. Warten Sie, bis der Prozess abgeschlossen ist, bevor Sie die Ergebnisse überprüfen.

```go
func deleteCluster(sub, clusterName, rgName string) {
    result, err := client.Delete(ctx, rgName, clusterName)
    ...
    err = result.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := result.Result(client)
    if r.StatusCode == 200 {
        // determine success or failure
    }
    ...
}
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Wenn Sie den Beispielcode unverändert ausführen, werden folgende Aktionen ausgeführt:
    
1. Ein Azure Data Explorer-Cluster wird erstellt.
1. Alle Azure Data Explorer-Cluster in der angegebenen Ressourcengruppe werden aufgelistet.
1. Im vorhandenen Cluster wird eine Azure Data Explorer-Datenbank erstellt.
1. Alle Datenbanken im angegebenen Cluster werden aufgelistet.
1. Die Datenbank wird gelöscht.
1. Der Cluster wird gelöscht.


    ```go
    func main() {
        createCluster(subscription, clusterNamePrefix+clusterName, location, rgName)
        listClusters(subscription, rgName)
        createDatabase(subscription, rgName, clusterNamePrefix+clusterName, location, dbNamePrefix+databaseName)
        listDatabases(subscription, rgName, clusterNamePrefix+clusterName)
        deleteDatabase(subscription, rgName, clusterNamePrefix+clusterName, dbNamePrefix+databaseName)
        deleteCluster(subscription, clusterNamePrefix+clusterName, rgName)
    }
    ```

    > [!TIP]
    > Sie können verschiedene Vorgangskombinationen ausprobieren, indem Sie die entsprechenden Funktionen in `main.go` nach Bedarf auskommentieren bzw. kommentieren.

1. Klonen Sie den Beispielcode von GitHub:

    ```console
    git clone https://github.com/Azure-Samples/azure-data-explorer-go-cluster-management.git
    cd azure-data-explorer-go-cluster-management
    ```

1. Das Programm wird mithilfe von Anmeldeinformationen authentifiziert. Erstellen Sie mithilfe des Azure CLI-Befehls [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) einen Dienstprinzipal. Speichern Sie die Client-ID, den geheimen Clientschlüssel und die Mandanten-ID-Informationen für die Verwendung im nächsten Schritt.

1. Exportieren Sie erforderliche Umgebungsvariablen, einschließlich Dienstprinzipalinformationen. Geben Sie die Abonnement-ID, die Ressourcengruppe und die Region ein, in der Sie den Cluster erstellen möchten.

    ```console
    export AZURE_CLIENT_ID="<enter service principal client ID>"
    export AZURE_CLIENT_SECRET="<enter service principal client secret>"
    export AZURE_TENANT_ID="<enter tenant ID>"

    export SUBSCRIPTION="<enter subscription ID>"
    export RESOURCE_GROUP="<enter resource group name>"
    export LOCATION="<enter azure location e.g. Southeast Asia>"

    export CLUSTER_NAME_PREFIX="<enter prefix (cluster name will be [prefix]-ADXTestCluster)>"
    export DATABASE_NAME_PREFIX="<enter prefix (database name will be [prefix]-ADXTestDB)>"
    ```
    
    > [!TIP]
    > In Produktionsszenarien verwenden Sie wahrscheinlich Umgebungsvariablen, um Anmeldeinformationen für Ihre Anwendung bereitzustellen. Wie unter [Überprüfen des Codes](#review-the-code) beschrieben, verwenden Sie für die lokale Entwicklung die Funktion [auth.NewAuthorizerFromCLIWithResource](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromCLIWithResource), wenn Sie die Azure CLI installiert und für die Authentifizierung konfiguriert haben. In diesem Fall müssen Sie keinen Dienstprinzipal erstellen.


1. Führen Sie das Programm aus:

    ```console
    go run main.go
    ```

    Die Ausgabe sieht in etwa wie folgt aus:

    ```console
    waiting for cluster creation to complete - fooADXTestCluster
    created cluster fooADXTestCluster
    listing clusters in resource group <your resource group>
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    |       NAME        |  STATE  |    LOCATION    | INSTANCES |                            URI                           |
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    | fooADXTestCluster | Running | Southeast Asia |         1 | https://fooADXTestCluster.southeastasia.kusto.windows.net |
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    
    waiting for database creation to complete - barADXTestDB
    created DB fooADXTestCluster/barADXTestDB with ID /subscriptions/<your subscription ID>/resourceGroups/<your resource group>/providers/Microsoft.Kusto/Clusters/fooADXTestCluster/Databases/barADXTestDB and type Microsoft.Kusto/Clusters/Databases
    
    listing databases in cluster fooADXTestCluster
    +--------------------------------+-----------+----------------+------------------------------------+
    |              NAME              |   STATE   |    LOCATION    |                TYPE                |
    +--------------------------------+-----------+----------------+------------------------------------+
    | fooADXTestCluster/barADXTestDB | Succeeded | Southeast Asia | Microsoft.Kusto/Clusters/Databases |
    +--------------------------------+-----------+----------------+------------------------------------+
    
    waiting for database deletion to complete - barADXTestDB
    deleted DB barADXTestDB from cluster fooADXTestCluster

    waiting for cluster deletion to complete - fooADXTestCluster
    deleted ADX cluster fooADXTestCluster from resource group <your resource group>
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Cluster nicht programmgesteuert mithilfe des Beispielcodes in diesem Artikel gelöscht haben, löschen Sie ihn mithilfe der [Azure CLI](create-cluster-database-cli.md#clean-up-resources) manuell.

## <a name="next-steps"></a>Nächste Schritte

[Erfassen von Daten mit dem Go SDK für Azure Data Explorer](go-ingest-data.md)
