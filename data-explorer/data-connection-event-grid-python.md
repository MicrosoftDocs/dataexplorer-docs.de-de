---
title: Erstellen einer Event Grid-Datenverbindung für Azure Data Explorer mit Python
description: In diesem Artikel erfahren Sie, wie Sie eine Event Grid-Datenverbindung für Azure Data Explorer mit Python erstellen.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 6e8bb6797200c3f3591fa8bf46e9b3ff7d4da49f
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013886"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>Erstellen einer Event Grid-Datenverbindung für Azure Data Explorer mit Python

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-Vorlage](data-connection-event-grid-resource-manager.md)

In diesem Artikel erstellen Sie eine Event Grid-Datenverbindung für Azure Data Explorer mit Python. Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Azure Data Explorer bietet Erfassung (oder Laden von Daten) aus Event Hubs, IoT Hubs und in Blobcontainer geschriebenen Blobs.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.4 oder höher](https://www.python.org/downloads/)

* [Ein Cluster und eine Datenbank](create-cluster-database-python.md).

* [Tabellen- und Spaltenzuordnung](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

* [Datenbank- und Tabellenrichtlinien](database-table-policies-csharp.md) (optional).

* [Speicherkonto mit einem Event Grid-Abonnement](../data-explorer/kusto/management/data-ingestion/eventgrid.md#create-an-event-grid-subscription-in-your-storage-account)

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Hinzufügen einer Event Grid-Datenverbindung

Im folgenden Beispiel wird gezeigt, wie eine Event Grid-Datenverbindung programmgesteuert hinzugefügt wird. Weitere Informationen zum Hinzufügen einer Event Grid-Datenverbindung mithilfe des Azure-Portals finden Sie unter [Erstellen einer Event Grid-Datenbankverbindung in Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```
|**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ihre Mandanten-ID. Wird auch als Verzeichnis-ID bezeichnet.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Abonnement-ID, die Sie für die Ressourcenerstellung verwenden.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Client-ID der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann.|
| client_secret | *xxxxxxxxxxxxxx* | Das Clientgeheimnis der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann. |
| resource_group_name | *testrg* | Der Name der Ressourcengruppe, die Ihren Cluster enthält.|
| cluster_name | *mykustocluster* | Der Name Ihres Clusters.|
| database_name | *mykustodatabase* | Der Name der Zieldatenbank in Ihrem Cluster.|
| data_connection_name | *myeventhubconnect* | Der gewünschte Name Ihrer Datenverbindung.|
| table_name | *StormEvents* | Der Name der Zieltabelle in der Zieldatenbank.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Der Name der Spaltenzuordnung, die mit der Zieltabelle verknüpft ist.|
| data_format | *csv* | Das Datenformat der Nachricht.|
| event_hub_resource_id | *Ressourcen-ID* | Die Ressourcen-ID Ihres Event Hubs, in dem Event Grid für das Senden von Ereignissen konfiguriert ist. |
| storage_account_resource_id | *Ressourcen-ID* | Die Ressourcen-ID Ihres Speicherkontos, das die Daten für die Erfassung enthält. |
| consumer_group | *$Default* | Die Consumergruppe Ihres Event Hubs.|
| location | *USA, Mitte* | Der Speicherort der Datenverbindungsressource.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]
