---
title: End-to-End-Bloberfassung in Azure Data Explorer über Python
description: In diesem Artikel erfahren Sie anhand eines vollständigen Beispiels in Python, wie Sie Blobs in Azure Data Explorer erfassen.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: bc97ffad3ca4f6d824e10c85e570154764cc986a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350467"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-through-python"></a>End-to-End-Bloberfassung in Azure Data Explorer über Python

> [!div class="op_single_selector"]
> * [C#](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

Azure Data Explorer ist ein schneller und skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Dieser Artikel enthält ein End-to-End-Beispiel für das Erfassen von Daten aus Azure Blob Storage in Azure Data Explorer. 

Sie erfahren, wie Sie eine Ressourcengruppe, ein Speicherkonto und einen Container, einen Event Hub, einen Azure Data Explorer-Cluster und eine Azure Data Explorer-Datenbank programmgesteuert erstellen. Außerdem erfahren Sie, wie Sie Azure Data Explorer programmgesteuert für die Erfassung von Daten aus dem neuen Speicherkonto konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="install-the-python-package"></a>Installieren des Python-Pakets

Um das Python-Paket für Azure Data Explorer (Kusto) zu installieren, öffnen Sie eine Eingabeaufforderung, deren Pfad Python enthält: Führen Sie diese Befehle aus.

```
pip install azure-common
pip install azure-mgmt-resource
pip install azure-mgmt-kusto
pip install azure-mgmt-eventgrid
pip install azure-kusto-data
pip install azure-storage-blob
```
[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>Codebeispiel 

Im folgenden Codebeispiel werden Sie in einzelnen Schritten durch das Verfahren für die Datenerfassung in Azure Data Explorer geführt. 

Zunächst erstellen Sie eine Ressourcengruppe. Außerdem erstellen Sie Azure-Ressourcen, z. B. ein Speicherkonto und einen Container, einen Event Hub sowie einen Azure Data Explorer-Cluster und eine Azure Data Explorer-Datenbank und fügen Prinzipale hinzu. Anschließend erstellen Sie ein Azure Event Grid-Abonnement zusammen mit einer Tabellen- und Spaltenzuordnung in der Azure Data Explorer-Datenbank. Abschließend erstellen Sie die Datenverbindung, um Azure Data Explorer für die Erfassung von Daten aus dem neuen Speicherkonto zu konfigurieren.

```python
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.resource.resources.models import DeploymentMode
import os.path
import json
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.mgmt.eventgrid import EventGridManagementClient
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
location = "West Europe"
location_small_case = "westeurope"
#Path to the Azure Resource Manager template JSON from the previous section
azure_resource_template_path = "xxxxxxxxx/template.json";

deployment_name = 'e2eexample'
resource_group_name = deployment_name + "resourcegroup"
event_hub_name = deployment_name + "eventhub"
event_hub_namespace_name = event_hub_name + "ns"
storage_account_name = deployment_name + "storage"
storage_container_name = deployment_name + "storagecontainer"
event_grid_subscription_name = deployment_name + "eventgrid"
kusto_cluster_name = deployment_name + "kustocluster"
kusto_database_name = deployment_name + "kustodatabase"
kusto_table_name = "Events"
kusto_column_mapping_name = "Events_CSV_Mapping"
kusto_data_connection_name = deployment_name + "kustoeventgridconnection"

#principals
principal_id_for_cluster = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
role_for_cluster_principal = "AllDatabasesAdmin";
tenant_id_for_cluster_principal = tenant_id;
principal_type_for_cluster = "App";
principal_id_for_database = "xxxxxxxx@xxxxxxxx.com";//User Email
role_for_database_principal = "Admin";
tenant_id_for_database_principal = tenant_id;
principal_type_for_database = "User";


credentials = ServicePrincipalCredentials(
    client_id=client_id,
    secret=client_secret,
    tenant=tenant_id
)
resource_client = ResourceManagementClient(credentials, subscription_id)

print('Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.')
resource_client.resource_groups.create_or_update(
    resource_group_name,
    {
        'location': location_small_case
    }
)

print('Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, database, and add principals by using an Azure Resource Manager template.')
#Read the Azure Resource Manager template
with open(azure_resource_template_path, 'r') as template_file_fd:
    template = json.load(template_file_fd)

parameters = {
    'eventHubNamespaceName': event_hub_namespace_name,
    'eventHubName': event_hub_name,
    'storageAccountName': storage_account_name,
    'containerName': storage_container_name,
    'kustoClusterName': kusto_cluster_name,
    'kustoDatabaseName': kusto_database_name,
    'principalIdForCluster': principal_id_for_cluster,
    'roleForClusterPrincipal': role_for_cluster_principal,
    'tenantIdForClusterPrincipal': tenant_id_for_cluster_principal,
    'principalTypeForCluster': principal_type_for_cluster,
    'principalIdForDatabase': principal_id_for_database,
    'roleForDatabasePrincipal': role_for_database_principal,
    'tenantIdForDatabasePrincipal': tenant_id_for_database_principal,
    'principalTypeForDatabase': principal_type_for_database
}
parameters = {k: {'value': v} for k, v in parameters.items()}
deployment_properties = {
    'mode': DeploymentMode.incremental,
    'template': template,
    'parameters': parameters
}

#Returns an instance of LROPoller; see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = resource_client.deployments.create_or_update(
    resource_group_name,
    deployment_name,
    deployment_properties
)
poller.wait()

print('Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.')
event_client = EventGridManagementClient(credentials, subscription_id)
storage_resource_id = '/subscriptions/{}/resourceGroups/{}/providers/Microsoft.Storage/storageAccounts/{}'.format(subscription_id, resource_group_name, storage_account_name)
event_hub_resource_id = '/subscriptions/{}/resourceGroups/{}/providers/Microsoft.EventHub/namespaces/{}/eventhubs/{}'.format(subscription_id, resource_group_name, event_hub_namespace_name, event_hub_name)
event_client.event_subscriptions.create_or_update(storage_resource_id, event_grid_subscription_name, {
    'destination': {
        'endpointType': 'EventHub',
        'properties': {
            'resourceId': event_hub_resource_id
        }
    },
    "filter": {
        "subjectBeginsWith": "/blobServices/default/containers/{}".format(storage_container_name),
        "includedEventTypes": ["Microsoft.Storage.BlobCreated"],
        "advancedFilters": []
    }
})


print('Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.')
kusto_uri = "https://{}.{}.kusto.windows.net".format(kusto_cluster_name, location_small_case)
database_name = kusto_database_name
kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)
kusto_client = KustoClient(kusto_connection_string_builder)
create_table_command = ".create table " + kusto_table_name + " (EventTime: datetime, EventId: int, EventSummary: string)"
kusto_client.execute_mgmt(database_name, create_table_command)

create_column_mapping_command = ".create table " + kusto_table_name + " ingestion csv mapping '" + kusto_column_mapping_name \
                                + """' '[{"Name":"EventTime","datatype":"datetime","Ordinal":0},{"Name":"EventId","datatype":"int","Ordinal":1},{"Name":"EventSummary","datatype":"string","Ordinal":2}]'"""
kusto_client.execute_mgmt(database_name, create_column_mapping_command)


print('Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.')
kusto_management_client = KustoManagementClient(credentials, subscription_id)
data_connections = kusto_management_client.data_connections
#Returns an instance of LROPoller; see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=kusto_cluster_name, database_name=kusto_database_name, data_connection_name=kusto_data_connection_name,
                                           parameters=EventGridDataConnection(storage_account_resource_id=storage_resource_id,
                                                                              event_hub_resource_id=event_hub_resource_id, consumer_group="$Default", location=location, table_name=kusto_table_name, mapping_rule_name=kusto_column_mapping_name, data_format="csv"))
poller.wait()
```
|**Einstellung** | **Feldbeschreibung**|
|---|---|---|
| tenant_id | Ihre Mandanten-ID. Diese wird auch als Verzeichnis-ID bezeichnet.|
| subscription_id | Die Abonnement-ID, die Sie für die Ressourcenerstellung verwenden.|
| client_id | Die Client-ID der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann.|
| client_secret | Das Clientgeheimnis der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann. |

## <a name="test-the-code-example"></a>Testen des Codebeispiels

1. Laden Sie eine Datei in das Speicherkonto hoch.

    ```python
    account_key = "xxxxxxxxxxxxxx"
    block_blob_service = BlockBlobService(account_name=storage_account_name, account_key=account_key)
    blob_name = "test.csv"
    blob_content = """2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm"""
    block_blob_service.create_blob_from_text(container_name=storage_container_name, blob_name=blob_name, text=blob_content)
    ```
    |**Einstellung** | **Feldbeschreibung**|
    |---|---|---|
    | account_key | Der Zugriffsschlüssel für das programmgesteuert erstellte Speicherkonto.|

2. Führen Sie in Azure Data Explorer eine Testabfrage aus.

    ```python
    kusto_uri = "https://{}.{}.kusto.windows.net".format(kusto_cluster_name, location_small_case)
    kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)
    kusto_client = KustoClient(kusto_connection_string_builder)
    query = "{} | take 10".format(kusto_table_name)
    response = kusto_client.execute_query(kusto_database_name, query)
    print(response.primary_results[0].rows_count)
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die Ressourcengruppe zu löschen und die Ressourcen zu entfernen, führen Sie den folgenden Befehl aus:

```python
#Returns an instance of LROPoller; see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = resource_client.resource_groups.delete(resource_group_name=resource_group_name)
poller.wait()
```

## <a name="next-steps"></a>Nächste Schritte

*  Weitere Informationen zu anderen Möglichkeiten zum Erstellen eines Clusters und einer Datenbank finden Sie unter [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](create-cluster-database-python.md).
* Unter [Datenerfassung in Azure Data Explorer](ingest-data-overview.md) erfahren Sie mehr über Erfassungsmethoden.
* Weitere Informationen zur Webanwendung finden Sie unter [Schnellstart: Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche](web-query-data.md).
* [Schreiben von Abfragen](write-queries.md) mit der Kusto-Abfragesprache