---
title: Konfigurieren der Streamingerfassung in Ihrem Azure Data Explorer-Cluster mithilfe von C#
description: Hier erfahren Sie, wie Sie Ihren Azure Data Explorer-Cluster konfigurieren und mit dem Laden von Daten mit der Streamingerfassung mithilfe von C# beginnen.
author: orspod
ms.author: orspodek
ms.reviewer: alexefro
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 73d1308ab38cf032eca52f772627f0946b5ea252
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2020
ms.locfileid: "86424245"
---
# <a name="configure-streaming-ingestion-on-your-azure-data-explorer-cluster-using-c"></a>Konfigurieren der Streamingerfassung in Ihrem Azure Data Explorer-Cluster mithilfe von C#

> [!div class="op_single_selector"]
> * [Portal](ingest-data-streaming.md)
> * [C#](ingest-data-streaming-csharp.md)

[!INCLUDE [ingest-data-streaming-intro](includes/ingest-data-streaming-intro.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Falls Sie Visual Studio 2019 nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2019 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden.  Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**.
* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Erstellen Sie [einen Azure Data Explorer-Cluster und eine Datenbank](create-cluster-database-csharp.md).
   
## <a name="enable-streaming-ingestion-on-your-cluster-using-c"></a>Aktivieren der Streamingerfassung in Ihrem Cluster mithilfe von C#

### <a name="enable-streaming-ingestion-while-creating-a-new-cluster"></a>Aktivieren der Streamingerfassung beim Erstellen eines neuen Clusters

Sie können die Streamingerfassung aktivieren, während Sie einen neuen Azure Data Explorer-Cluster erstellen.

```csharp
...
var cluster = new Cluster(location, sku, enableStreamingIngest:true);
...
```

### <a name="enable-streaming-ingestion-on-an-existing-cluster"></a>Aktivieren der Streamingerfassung für einen vorhandenen Cluster

Führen Sie zum Aktivieren der Streamingerfassung in Ihrem Azure Data Explorer-Cluster den folgenden Code aus:

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
    var clusterName = "mystreamingcluster";
    var clusterUpdateParameters = new ClusterUpdate(enableStreamingIngest: true);

    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdateParameters);
```

> [!WARNING]
> Überprüfen Sie vor dem Aktivieren der Streamingerfassung die [Einschränkungen](#limitations).

## <a name="create-a-target-table-and-define-the-policy-using-c"></a>Erstellen einer Zieltabelle und Definieren der Richtlinie mithilfe von C#

Führen Sie den folgenden Code aus, um eine Tabelle zu erstellen und eine Streamingerfassungsrichtlinie für diese Tabelle zu definieren:

```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var databaseName = "StreamingTestDb";
    var tableName = "TestTable";
    var kcsb = new KustoConnectionStringBuilder("https://mystreamingcluster.westcentralus.kusto.windows.net", databaseName);
    kcsb = kcsb.WithAadApplicationKeyAuthentication(clientId, clientSecret, tenantId);

    var tableSchema = new TableSchema(
        tableName,
        new ColumnSchema[]
        {
            new ColumnSchema("TimeStamp", "System.DateTime"),
            new ColumnSchema("Name",      "System.String"),
            new ColumnSchema("Metric",    "System.int"),
            new ColumnSchema("Source",    "System.String"),
        });

    var tableCreateCommand = CslCommandGenerator.GenerateTableCreateCommand(tableSchema);
    var tablePolicyAlterCommand = CslCommandGenerator.GenerateTableAlterStreamingIngestionPolicyCommand(tableName, isEnabled: true);
    using (var client = KustoClientFactory.CreateCslAdminProvider(kcsb))
    {
        client.ExecuteControlCommand(tableCreateCommand);

        client.ExecuteControlCommand(tablePolicyAlterCommand);
    }
```

[!INCLUDE [ingest-data-streaming-use](includes/ingest-data-streaming-types.md)]

[!INCLUDE [ingest-data-streaming-disable](includes/ingest-data-streaming-disable.md)]

### <a name="drop-the-streaming-ingestion-policy-using-c"></a>Löschen der Streamingerfassungsrichtlinie mithilfe von C#

Führen Sie den folgenden Code aus, um die Streamingerfassungsrichtlinie aus der Tabelle zu entfernen:
    
```csharp
        var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
        var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
        var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
        var databaseName = "StreamingTestDb";
        var tableName = "TestTable";
        var kcsb = new KustoConnectionStringBuilder("https://mystreamingcluster.westcentralus.kusto.windows.net", databaseName);
        kcsb = kcsb.WithAadApplicationKeyAuthentication(clientId, clientSecret, tenantId);
    
        var tablePolicyDropCommand = CslCommandGenerator.GenerateTableStreamingIngestionPolicyDropCommand(databaseName, tableName);
        using (var client = KustoClientFactory.CreateCslAdminProvider(kcsb))
        {
            client.ExecuteControlCommand(tablePolicyDropCommand);
        }
```

### <a name="disable-streaming-ingestion-using-c"></a>Deaktivieren der Streamingerfassung mithilfe von C#

Führen Sie den folgenden Code aus, um die Streamingerfassungsrichtlinie für Ihren Cluster zu deaktivieren:
    
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
        var clusterName = "mystreamingcluster";
        var clusterUpdateParameters = new ClusterUpdate(enableStreamingIngest: false);
    
        await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdateParameters);
```
    
[!INCLUDE [ingest-data-streaming-limitations](includes/ingest-data-streaming-limitations.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten in Azure Data Explorer](web-query-data.md)
