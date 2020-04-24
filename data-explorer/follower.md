---
title: Verwenden der Follower-Datenbankfunktion zum Anfügen von Datenbanken in Azure Data Explorer
description: Erfahren Sie, wie Sie Datenbanken in Azure Data Explorer mithilfe der Follower-Datenbankfunktion anfügen.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 4c385551f8983219dddf840f7a2d590809b030a3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492758"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Verwenden der Follower-Datenbank zum Anfügen von Datenbanken in Azure Data Explorer

Mit der Funktion **Follower-Datenbank** können Sie eine Datenbank, die sich in einem anderen Cluster befindet, an Ihren Azure Data Explorer-Cluster anfügen. Die **Follower-Datenbank** wird im *schreibgeschützten* Modus angefügt, sodass Sie die Daten anzeigen und Abfragen für die Daten ausführen können, die in der **Leader-Datenbank** erfasst wurden. Die Follower-Datenbank synchronisiert Änderungen in den Leader-Datenbanken. Aufgrund der Synchronisierung gibt es bei der Datenverfügbarkeit eine Zeitverzögerung von einigen Sekunden bis zu einigen Minuten. Die Länge der Zeitverzögerung hängt von der Gesamtgröße der Metadaten in der Leader-Datenbank ab. Die Leader- und Follower-Datenbanken verwenden dasselbe Speicherkonto zum Abrufen der Daten. Der Speicher befindet sich im Besitz der Leader-Datenbank. Die Follower-Datenbank zeigt die Daten an, ohne sie erfassen zu müssen. Da die angefügte Datenbank eine schreibgeschützte Datenbank ist, können die Daten, Tabellen und Richtlinien in der Datenbank nicht geändert werden, mit Ausnahme der [Cacherichtlinie](#configure-caching-policy), [Prinzipale](#manage-principals) und [Berechtigungen](#manage-permissions). Angefügte Datenbanken können nicht gelöscht werden. Zum Löschen müssen sie zunächst vom Leader- oder Follower-Cluster getrennt werden. 

Das Anfügen einer Datenbank an einen anderen Cluster mithilfe der Follower-Funktion wird als Infrastruktur zum Freigeben von Daten zwischen Organisationen und Teams verwendet. Die Funktion ist nützlich, um Computeressourcen zu trennen und so eine Produktionsumgebung vor nicht produktionsbezogenen Anwendungsfällen zu schützen. Follower können auch verwendet werden, um die Kosten eines Azure Data Explorer-Clusters demjenigen zuzuordnen, der Abfragen für die Daten ausführt.

## <a name="which-databases-are-followed"></a>Welche Datenbanken wird gefolgt?

* Ein Cluster kann einer Datenbank, mehreren Datenbanken oder allen Datenbanken eines Leader-Clusters folgen. 
* Ein einzelner Cluster kann Datenbanken von mehreren Leader-Clustern folgen. 
* Ein Cluster kann sowohl Follower- als auch Leader-Datenbanken enthalten.

## <a name="prerequisites"></a>Voraussetzungen

1. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
1. [Erstellen Sie Cluster und Datenbank](/azure/data-explorer/create-cluster-database-portal) für den Leader und den Follower.
1. [Erfassen Sie Daten](/azure/data-explorer/ingest-sample-data) in der Leader-Datenbank mithilfe einer der verschiedenen Methoden, die unter [Übersicht über die Datenerfassung](/azure/data-explorer/ingest-data-overview) erläutert werden.

## <a name="attach-a-database"></a>Anfügen einer Datenbank

Es gibt verschiedene Methoden zum Anfügen einer Datenbank. In diesem Artikel wird das Anfügen einer Datenbank mithilfe von C# oder einer Azure Resource Manager-Vorlage erläutert. Zum Anfügen einer Datenbank müssen Sie über Berechtigungen für den Leader-Cluster und den Follower-Cluster verfügen. Weitere Informationen zu Berechtigungen finden Sie unter [Verwalten von Berechtigungen](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Anfügen einer Datenbank mithilfe von C#

#### <a name="needed-nugets"></a>Erforderliche NuGet-Pakete

* Installieren Sie [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installieren Sie [Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) für die Authentifizierung.

#### <a name="code-example"></a>Codebeispiel

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>Anfügen einer Datenbank mithilfe von Python

#### <a name="needed-modules"></a>Erforderliche Module

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Codebeispiel

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Anhängen einer Datenbank mithilfe einer Azure Resource Manager-Vorlage

In diesem Abschnitt erfahren Sie, wie Sie eine Datenbank mithilfe einer [Azure Resource Manager-Vorlage](/azure/azure-resource-manager/management/overview) an einen vorhandenen Cluster anfügen. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>Bereitstellen der Vorlage 

Sie können die Azure Resource Manager-Vorlage über das [Azure-Portal](https://portal.azure.com) oder mithilfe von PowerShell bereitstellen.

   ![Vorlagenbereitstellung](media/follower/template-deployment.png)


|**Einstellung**  |**Beschreibung**  |
|---------|---------|
|Name des Follower-Clusters     |  Der Name des Followerclusters, auf dem die Vorlage bereitgestellt wird.  |
|Name der angehängten Datenbankkonfigurationen    |    Der Name des Objekts für angefügte Datenbankkonfigurationen. Der Name kann eine beliebige Zeichenfolge sein, die auf Clusterebene eindeutig ist.     |
|Datenbankname     |      Der Name der zu folgenden Datenbank. Wenn Sie allen Datenbanken des Leaders folgen möchten, verwenden Sie „*“.   |
|Leader-Clusterressourcen-ID    |   Die Ressourcen-ID des Leader-Clusters.      |
|Standardänderungsart für Prinzipale    |   Die Standardänderungsart für Prinzipale. Dies kann `Union`, `Replace` oder `None` sein. Weitere Informationen zur Standardänderungsart für Prinzipale finden Sie unter [Steuerungsbefehl für Prinzipaländerungsart](kusto/management/cluster-follower.md#alter-follower-database-principals-modification-kind).      |
|Position   |   Der Speicherort aller Ressourcen. Der Leader und der Follower müssen sich am gleichen Speicherort befinden.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Überprüfen, ob die Datenbank erfolgreich angehängt wurde

Um zu überprüfen, ob die Datenbank erfolgreich angefügt wurde, suchen Sie im [Azure-Portal](https://portal.azure.com) nach angefügten Datenbanken. 

1. Navigieren Sie zum Follower-Cluster, und wählen Sie **Datenbanken** aus.
1. Suchen Sie in der Datenbankliste nach neuen schreibgeschützten Datenbanken.

    ![Schreibgeschützte Follower-Datenbank](media/follower/read-only-follower-database.png)

Alternativ:

1. Navigieren Sie zum Leader-Cluster, und wählen Sie **Datenbanken** aus.
2. Überprüfen Sie, ob die relevanten Datenbanken unter **FREIGEGEBEN FÜR WEITERE PERSONEN** >  mit **Ja** gekennzeichnet sind.

    ![Lesen und Schreiben für angefügte Datenbanken](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Trennen der Follower-Datenbank mithilfe vonC# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Trennen der angefügten Follower-Datenbank vom Follower-Cluster

Vom Follower-Cluster kann jede angefügte Datenbank wie folgt getrennt werden:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Trennen der angefügten Follower-Datenbank vom Leader-Cluster

Vom Leader-Cluster kann jede angefügte Datenbank wie folgt getrennt werden:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Trennen der Follower-Datenbank mithilfe von Python

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Trennen der angefügten Follower-Datenbank vom Follower-Cluster

Vom Follower-Cluster kann jede angefügte Datenbank wie folgt getrennt werden:

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Trennen der angefügten Follower-Datenbank vom Leader-Cluster

Vom Leader-Cluster kann jede angefügte Datenbank wie folgt getrennt werden:

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Verwalten von Prinzipalen, Berechtigungen und Cacherichtlinie

### <a name="manage-principals"></a>Verwalten von Prinzipalen

Wenn Sie eine Datenbank anfügen, geben Sie die **Standardänderungsart für Prinzipale** an. Die Standardeinstellung ist die Beibehaltung der Leader-Datenbanksammlung von [autorisierten Prinzipalen](kusto/management/access-control/index.md#authorization).

|**Kind** |**Beschreibung**  |
|---------|---------|
|**Union**     |   Die angefügten Datenbankprinzipale umfassen immer die ursprünglichen Datenbankprinzipale und zusätzliche neue Prinzipale, die der Follower-Datenbank hinzugefügt werden.      |
|**Replace**   |    Keine Vererbung von Prinzipalen aus der ursprünglichen Datenbank. Für die angefügte Datenbank müssen neue Prinzipale erstellt werden.     |
|**None**   |   Die angefügten Datenbankprinzipale umfassen nur die Prinzipale der ursprünglichen Datenbank ohne zusätzliche Prinzipale.      |

Weitere Informationen zur Verwendung von Steuerungsbefehlen zum Konfigurieren der autorisierten Prinzipale finden Sie unter [Steuerungsbefehle zum Verwalten eines Follower-Clusters](kusto/management/cluster-follower.md).

### <a name="manage-permissions"></a>Verwalten von Berechtigungen

Das Verwalten der Berechtigung für schreibgeschützte Datenbanken entspricht der Vorgehensweise bei allen Datenbanktypen. Informationen dazu finden Sie unter [Verwalten von Berechtigungen im Azure-Portal](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Konfigurieren der Cacherichtlinie

Der Administrator der Follower-Datenbank kann die [Cacherichtlinie](kusto/management/cache-policy.md) der angefügten Datenbank oder einer der zugehörigen Tabellen im Hostingcluster ändern. Die Standardeinstellung ist die Beibehaltung der Leader-Datenbanksammlung von Cacherichtlinien auf Datenbank- und Tabellenebene. Sie können beispielsweise über eine 30-Tage-Cacherichtlinie für die Leader-Datenbank zur monatlichen Berichterstellung und eine 3-Tage-Cacherichtlinie für die Follower-Datenbank zum ausschließlichen Abfragen der aktuellen Daten für die Problembehandlung verfügen. Weitere Informationen zur Verwendung von Steuerungsbefehlen zum Konfigurieren der Cacherichtlinie für die Follower-Datenbank oder -Tabelle finden Sie unter [Steuerungsbefehle zum Verwalten eines Follower-Clusters](kusto/management/cluster-follower.md).

## <a name="limitations"></a>Einschränkungen

* Die Leader- und Follower-Cluster müssen sich in derselben Region befinden.
* Die [Streamingerfassung](/azure/data-explorer/ingest-data-streaming) kann für eine Datenbank, der gefolgt wird, nicht verwendet werden.
* Datenverschlüsselung mithilfe von [kundenseitig verwalteten Schlüsseln](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault) wird auf Leader- und Follower-Clustern nicht unterstützt. 
* Sie können eine Datenbank, die an einen anderen Cluster angefügt ist, erst nach dem Trennen löschen.
* Sie können einen Cluster mit einer Datenbank, die an einen anderen Cluster angefügt ist, erst nach dem Trennen löschen.
* Sie können einen Cluster, der über angefügte Follower- oder Leader-Datenbanken verfügt, nicht beenden. 

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Konfiguration von Follower-Clustern finden Sie unter [Steuerungsbefehle zum Verwalten eines Follower-Clusters](kusto/management/cluster-follower.md).
