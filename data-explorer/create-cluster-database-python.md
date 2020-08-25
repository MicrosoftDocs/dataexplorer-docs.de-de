---
title: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mit Python
description: Hier erfahren Sie, wie Sie mit Python einen Azure Data Explorer-Cluster und eine Datenbank erstellen.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 60b485a7abe9f505da3ca32c4f35fbbfda101321
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793868"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mit Python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-Vorlage](create-cluster-database-resource-manager.md)

In diesem Artikel erfahren Sie, wie Sie mit Python einen Azure Data Explorer-Cluster und eine Datenbank erstellen. Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. Um den Azure Data Explorer zu verwenden, erstellen Sie zuerst einen Cluster und anschließend eine oder mehrere Datenbanken in diesem Cluster. Anschließend erfassen Sie Daten in einer Datenbank (laden), damit Sie diese abfragen können.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.4 oder höher](https://www.python.org/downloads/)

* [Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Rufen Sie Werte für `Directory (tenant) ID`, `Application ID` und `Client Secret` ab.

## <a name="install-python-package"></a>Installieren des Python-Pakets

Um das Python-Paket für Azure Data Explorer (Kusto) zu installieren, öffnen Sie eine Eingabeaufforderung, deren Pfad Python enthält: Führen Sie den folgenden Befehl aus:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Authentication
Zum Ausführen der Beispiele in diesem Artikel benötigen wir eine Azure AD-Anwendung und einen Dienstprinzipal, der auf Ressourcen zugreifen kann. Um eine kostenlose Azure AD-Anwendung zu erstellen und die Rollenzuweisung im Abonnementbereich hinzuzufügen, lesen Sie [Erstellen einer Azure AD-Anwendung](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Außerdem wird gezeigt, wie Sie die Angaben `Directory (tenant) ID`, `Application ID` und `Client Secret` abrufen.

## <a name="create-the-azure-data-explorer-cluster"></a>Erstellen des Azure Data Explorer-Clusters

1. Erstellen Sie Ihren Cluster mit dem folgenden Befehl:

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
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

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Der gewünschte Name Ihres Clusters.|
   | sku_name | *Standard_D13_v2* | Die SKU, die für Ihren Cluster verwendet wird. |
   | Ebene | *Standard* | Die SKU-Ebene. |
   | Kapazität | *Zahl* | Die Anzahl der Instanzen des Clusters. |
   | resource_group_name | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |

    > [!NOTE]
    > Das **Erstellen eines Clusters** ist ein zeitintensiver Vorgang. Die Methode **create_or_update** gibt eine Instanz von LROPoller zurück. Weitere Informationen finden Sie unter [LROPoller-Klasse](/python/api/msrest/msrest.polling.lropoller?view=azure-python).

1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Cluster erfolgreich erstellt wurde:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Wenn das Ergebnis `provisioningState` mit dem Wert `Succeeded` enthält, wurde der Cluster erfolgreich erstellt.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Erstellen der Datenbank im Azure Data Explorer-Cluster

1. Erstellen Sie Ihre Datenbank mit dem folgenden Befehl:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = ReadWriteDatabase(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

    > [!NOTE]
    > Wenn Sie Python-Version 0.4.0 oder eine niedrigere Version nutzen, verwenden Sie „Database“ anstelle von „ReadWriteDatabase“.

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Der Name Ihres Clusters, in dem die Datenbank erstellt werden soll.|
   | database_name | *mykustodatabase* | Der Name Ihrer Datenbank.|
   | resource_group_name | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |
   | soft_delete_period | *3650 days, 0:00:00* | Der Zeitraum, für den Daten für Abfragen verfügbar sein sollen. |
   | hot_cache_period | *3650 days, 0:00:00* | Der Zeitraum, für den Daten im Cache verfügbar sein sollen. |

1. Führen Sie den folgenden Befehl aus, um die erstellte Datenbank anzuzeigen:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Jetzt verfügen Sie über einen Cluster und eine Datenbank.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

* Wenn Sie unsere anderen Artikel durcharbeiten möchten, behalten Sie die erstellten Ressourcen bei.
* Löschen Sie den Cluster, um Ressourcen zu bereinigen. Wenn Sie einen Cluster löschen, werden auch alle darin enthaltenen Datenbanken gelöscht. Verwenden Sie den folgenden Befehl, um Ihren Cluster zu löschen:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Python](python-ingest-data.md)
