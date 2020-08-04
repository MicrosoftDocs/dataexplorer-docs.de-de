---
title: Erstellen einer IoT Hub-Datenverbindung für Azure Data Explorer mit C#
description: In diesem Artikel erfahren Sie, wie Sie eine IoT Hub-Datenverbindung für Azure Data Explorer mit C# erstellen.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: a77549d729b8ccba91333922e5add8a9296527c4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350110"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Erstellen einer IoT Hub-Datenverbindung für Azure Data Explorer mit C# (Vorschau)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-Vorlage](data-connection-iot-hub-resource-manager.md)

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Azure Data Explorer bietet Erfassung (Laden von Daten) aus Event Hubs, IoT Hubs und Blobs, die in Blobcontainer geschrieben werden. In diesem Artikel erstellen Sie eine IoT Hub-Datenverbindung für Azure Data Explorer mit C#.

## <a name="prerequisites"></a>Voraussetzungen

* Falls Sie Visual Studio 2019 nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2019 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**.
* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Erstellen eines [Clusters und einer Datenbank](create-cluster-database-csharp.md)
* Erstellen einer [Tabelle und Spaltenzuordnung](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Festlegen von [Datenbank- und Tabellenrichtlinien](database-table-policies-csharp.md) (optional)
* Erstellen Sie einen [IoT Hub mit einer konfigurierten SAS-Richtlinie](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Hinzufügen einer IoT Hub-Datenverbindung 

Im folgenden Beispiel wird gezeigt, wie eine IoT Hub-Datenverbindung programmgesteuert hinzugefügt wird. Weitere Informationen zum Hinzufügen einer IoT Hub-Datenverbindung mithilfe des Azure-Portals finden Sie unter [Herstellen einer Verbindung zwischen der Azure Data Explorer-Tabelle und IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub).

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
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ihre Mandanten-ID. Wird auch als Verzeichnis-ID bezeichnet.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Abonnement-ID, die Sie für die Ressourcenerstellung verwenden.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Client-ID der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann.|
| clientSecret | *xxxxxxxxxxxxxx* | Das Clientgeheimnis der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann. |
| resourceGroupName | *testrg* | Der Name der Ressourcengruppe, die Ihren Cluster enthält.|
| clusterName | *mykustocluster* | Der Name Ihres Clusters.|
| databaseName | *mykustodatabase* | Der Name der Zieldatenbank in Ihrem Cluster.|
| dataConnectionName | *myeventhubconnect* | Der gewünschte Name Ihrer Datenverbindung.|
| tableName | *StormEvents* | Der Name der Zieltabelle in der Zieldatenbank.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Der Name der Spaltenzuordnung, die mit der Zieltabelle verknüpft ist.|
| dataFormat | *csv* | Das Datenformat der Nachricht.|
| iotHubResourceId | *Ressourcen-ID* | Die Ressourcen-ID Ihres IoT-Hubs mit den Daten für die Erfassung. |
| sharedAccessPolicyName | *iothubforread* | Der Name der SAS-Richtlinie, die Berechtigungen für Geräte und Dienste zum Herstellen einer Verbindung mit IoT Hub definiert. |
| consumerGroup | *$Default* | Die Consumergruppe Ihres Event Hubs.|
| location | *USA, Mitte* | Der Speicherort der Datenverbindungsressource.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
