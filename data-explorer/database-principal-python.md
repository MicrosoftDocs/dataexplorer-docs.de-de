---
title: Hinzufügen von Datenbankprinzipalen für Azure Data Explorer mithilfe von Python
description: In diesem Artikel erfahren Sie, wie Sie Datenbankprinzipale für Azure Data Explorer mithilfe von Python hinzufügen.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 3ddb34692158fdc7e6d4f7786f84b8c059bc0621
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493366"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Hinzufügen von Datenbankprinzipalen für Azure Data Explorer mithilfe von Python

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager-Vorlage](database-principal-resource-manager.md)

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). In diesem Artikel fügen Sie Datenbankprinzipale für Azure Data Explorer mithilfe von Python hinzu.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* [Erstellen eines Clusters und einer Datenbank](create-cluster-database-python.md)

## <a name="install-python-package"></a>Installieren des Python-Pakets

Um das Python-Paket für Azure Data Explorer (Kusto) zu installieren, öffnen Sie eine Eingabeaufforderung, deren Pfad Python enthält: Führen Sie den folgenden Befehl aus:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Hinzufügen eines Datenbankprinzipals

Im folgenden Beispiel wird gezeigt, wie Sie einen Datenbankprinzipal programmgesteuert hinzufügen.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
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
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Ihre Mandanten-ID. Wird auch als Verzeichnis-ID bezeichnet.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Abonnement-ID, die Sie für die Ressourcenerstellung verwenden.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Client-ID der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann.|
| client_secret | *xxxxxxxxxxxxxx* | Das Clientgeheimnis der Anwendung, die auf Ressourcen in Ihrem Mandanten zugreifen kann. |
| resource_group_name | *testrg* | Der Name der Ressourcengruppe, die Ihren Cluster enthält.|
| cluster_name | *mykustocluster* | Der Name Ihres Clusters.|
| database_name | *mykustodatabase* | Der Name Ihrer Datenbank.|
| principal_assignment_name | *databasePrincipalAssignment1* | Der Name Ihrer Datenbankprinzipalressource.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Prinzipal-ID. Dies kann die E-Mail-Adresse des Benutzers, die Anwendungs-ID oder der Name der Sicherheitsgruppe sein.|
| Rolle (role) | *Administrator* | Die Rolle Ihres Datenbankprinzipals, die „Admin“, „Ingestor“, „Monitor“, „Benutzer“, „UnrestrictedViewers“'oder „Viewer“ sein kann.|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Die Mandanten-ID des Prinzipals.|
| principal_type | *App* | Der Typ des Prinzipals, der „Benutzer“, „App“ oder „Gruppe“ sein kann.|

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Python](python-ingest-data.md)
