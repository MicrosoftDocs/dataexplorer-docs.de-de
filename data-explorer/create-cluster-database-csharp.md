---
title: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mit C#
description: Hier erfahren Sie, wie Sie mit C# einen Azure Data Explorer-Cluster und eine Datenbank erstellen.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: 9182d4f5c12031cb4aaa62ab07bd00ab7864a627
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92902512"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mit C#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Go](create-cluster-database-go.md)
> * [Azure Resource Manager-Vorlage](create-cluster-database-resource-manager.md)

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. Um den Azure Data Explorer zu verwenden, erstellen Sie zuerst einen Cluster und anschließend eine oder mehrere Datenbanken in diesem Cluster. Anschließend erfassen (laden) Sie Daten in eine Datenbank, damit Sie diese abfragen können. In diesem Artikel erstellen Sie einen Cluster und eine Datenbank mit C#.

## <a name="prerequisites"></a>Voraussetzungen

* Falls Sie Visual Studio 2019 nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2019 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung** .
* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

## <a name="authentication"></a>Authentication
Zum Ausführen der Beispiele in diesem Artikel benötigen wir eine Azure AD-Anwendung und einen Dienstprinzipal, der auf Ressourcen zugreifen kann. Um eine kostenlose Azure AD-Anwendung zu erstellen und die Rollenzuweisung im Abonnementbereich hinzuzufügen, lesen Sie [Erstellen einer Azure AD-Anwendung](/azure/active-directory/develop/howto-create-service-principal-portal). Außerdem wird gezeigt, wie Sie die Angaben `Directory (tenant) ID`, `Application ID` und `Client Secret` abrufen.

## <a name="create-the-azure-data-explorer-cluster"></a>Erstellen des Azure Data Explorer-Clusters

1. Erstellen Sie Ihren Cluster mit dem folgenden Code:

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
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | clusterName | *mykustocluster* | Der gewünschte Name Ihres Clusters.|
   | skuName | *Standard_D13_v2* | Die SKU, die für Ihren Cluster verwendet wird. |
   | Ebene | *Standard* | Die SKU-Ebene. |
   | Kapazität | *Zahl* | Die Anzahl der Instanzen des Clusters. |
   | resourceGroupName | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |

    > [!NOTE]
    > Das **Erstellen eines Clusters** ist ein zeitintensiver Vorgang. Daher wird dringend empfohlen, anstelle von CreateOrUpdate die Option CreateOrUpdateAsync zu verwenden. 

1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Cluster erfolgreich erstellt wurde:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Wenn das Ergebnis `ProvisioningState` mit dem Wert `Succeeded` enthält, wurde der Cluster erfolgreich erstellt.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Erstellen der Datenbank im Azure Data Explorer-Cluster

1. Erstellen Sie Ihre Datenbank mit dem folgenden Code:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

    > [!NOTE]
    > Wenn Sie C#-Version 2.0.0 oder eine niedrigere Version nutzen, verwenden Sie „Database“ anstelle von „ReadWriteDatabase“.

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | clusterName | *mykustocluster* | Der Name Ihres Clusters, in dem die Datenbank erstellt werden soll.|
   | databaseName | *mykustodatabase* | Der Name Ihrer Datenbank.|
   | resourceGroupName | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |
   | softDeletePeriod | *3650:00:00:00* | Der Zeitraum, für den Daten für Abfragen verfügbar sein sollen. |
   | hotCachePeriod | *3650:00:00:00* | Der Zeitraum, für den Daten im Cache verfügbar sein sollen. |

2. Führen Sie den folgenden Befehl aus, um die erstellte Datenbank anzuzeigen:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

Jetzt verfügen Sie über einen Cluster und eine Datenbank.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

* Wenn Sie unsere anderen Artikel durcharbeiten möchten, behalten Sie die erstellten Ressourcen bei.
* Löschen Sie den Cluster, um Ressourcen zu bereinigen. Wenn Sie einen Cluster löschen, werden auch alle darin enthaltenen Datenbanken gelöscht. Verwenden Sie den folgenden Befehl, um Ihren Cluster zu löschen:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen von Daten mit dem .NET Standard SDK für Azure Data Explorer (Vorschauversion)](./net-sdk-ingest-data.md)