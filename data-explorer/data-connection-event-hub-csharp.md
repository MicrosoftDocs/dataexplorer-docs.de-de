---
title: Erstellen einer Event Hub-Datenverbindung für Azure Data Explorer mit C#
description: In diesem Artikel erfahren Sie, wie Sie eine Event Hub-Datenverbindung für Azure Data Explorer mit C# erstellen.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/07/2019
ms.openlocfilehash: c2cfe861898c2fa68960636b3c4bb4a2dc9b3075
ms.sourcegitcommit: f2f9cc0477938da87e0c2771c99d983ba8158789
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2020
ms.locfileid: "89502448"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Erstellen einer Event Hub-Datenverbindung für Azure Data Explorer mit C#

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager-Vorlage](data-connection-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
In diesem Artikel erstellen Sie eine Event Hub-Datenverbindung für Azure Data Explorer mit C#.

## <a name="prerequisites"></a>Voraussetzungen

* Falls Sie Visual Studio 2019 nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2019 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**.
* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Erstellen eines [Clusters und einer Datenbank](create-cluster-database-csharp.md)
* Erstellen einer [Tabelle und Spaltenzuordnung](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Festlegen von [Datenbank- und Tabellenrichtlinien](database-table-policies-csharp.md) (optional)
* Erstellen Sie einen [Event Hub mit Daten für die Erfassung](ingest-data-event-hub.md#create-an-event-hub). 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Hinzufügen einer Event Hub-Datenverbindung

Im folgenden Beispiel wird gezeigt, wie eine Event Hub-Datenverbindung programmgesteuert hinzugefügt wird. Weitere Informationen zum Hinzufügen einer Event Hub-Datenverbindung mit dem Azure-Portal finden Sie unter [Herstellen einer Verbindung mit dem Event Hub](ingest-data-event-hub.md#connect-to-the-event-hub).

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
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ihre Mandanten-ID. Wird auch als Verzeichnis-ID bezeichnet.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Abonnement-ID, die Sie für die Ressourcenerstellung verwenden.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Client-ID der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann.|
| clientSecret | *xxxxxxxxxxxxxx* | Das Clientgeheimnis der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann.|
| resourceGroupName | *testrg* | Der Name der Ressourcengruppe, die Ihren Cluster enthält.|
| clusterName | *mykustocluster* | Der Name Ihres Clusters.|
| databaseName | *mykustodatabase* | Der Name der Zieldatenbank in Ihrem Cluster.|
| dataConnectionName | *myeventhubconnect* | Der gewünschte Name Ihrer Datenverbindung.|
| tableName | *StormEvents* | Der Name der Zieltabelle in der Zieldatenbank.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Der Name der Spaltenzuordnung, die mit der Zieltabelle verknüpft ist.|
| dataFormat | *csv* | Das Datenformat der Nachricht.|
| eventHubResourceId | *Ressourcen-ID* | Die Ressourcen-ID Ihres Event Hubs mit den Daten für die Erfassung. |
| consumerGroup | *$Default* | Die Consumergruppe Ihres Event Hubs.|
| location | *USA, Mitte* | Der Speicherort der Datenverbindungsressource.|

## <a name="generate-data"></a>Generieren von Daten

Sehen Sie sich die [Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) an, die Daten generiert und an einen Event Hub sendet.

Ein Ereignis kann entsprechend dem Größenlimit einen oder mehrere Datensätze enthalten. Im folgenden Beispiel werden zwei Ereignisse mit jeweils fünf angefügten Datensätzen gesendet:

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) }; 
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
