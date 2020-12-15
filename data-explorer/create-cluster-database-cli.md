---
title: Erstellen eines Azure Data Explorer-Clusters und einer -Datenbank mit der Azure-Befehlszeilenschnittstelle
description: Hier erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle einen Azure Data Explorer-Cluster und eine Datenbank erstellen.
author: orspod
ms.author: orspodek
ms.reviewer: radennis
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: 4098f8bd6992ce5f76bfd3f611ba4d4bad2374b3
ms.sourcegitcommit: 79d923d7b7e8370726974e67a984183905f323ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96868653"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Erstellen eines Azure Data Explorer-Clusters und einer Datenbank über die Azure-Befehlszeilenschnittstelle

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Go](create-cluster-database-go.md)
> * [ARM-Vorlage](create-cluster-database-resource-manager.md)

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. Um den Azure Data Explorer zu verwenden, erstellen Sie zuerst einen Cluster und anschließend eine oder mehrere Datenbanken in diesem Cluster. Anschließend erfassen (laden) Sie Daten in eine Datenbank, damit Sie diese abfragen können. In diesem Artikel erstellen Sie einen Cluster und eine Datenbank über die Azure-Befehlszeilenschnittstelle.

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Artikel benötigen Sie ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

Wenn Sie die Azure-Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens die Azure CLI-Version 2.0.4 verwenden. Führen Sie `az --version` aus, um Ihre Version zu überprüfen. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

## <a name="configure-the-cli-parameters"></a>Konfigurieren der CLI-Parameter

Die folgenden Schritte sind nicht erforderlich, wenn Sie Befehle in Azure Cloud Shell ausführen. Wenn Sie die Befehlszeilenschnittstelle lokal ausführen, gehen Sie wie folgt vor, um sich bei Azure anzumelden und Ihr aktuelles Abonnement festzulegen:

1. Führen Sie den folgenden Befehl aus, um sich bei Azure anzumelden:

    ```azurecli-interactive
    az login
    ```

1. Legen Sie das Abonnement fest, in dem der Cluster erstellt werden soll. Ersetzen Sie `MyAzureSub` durch den Namen des gewünschten Azure-Abonnements:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```
   
1. Installieren Sie die Erweiterung, um die aktuelle Version der Kusto-Befehlszeilenschnittstelle zu verwenden:

    ```azurecli-interactive
    az extension add -n kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Erstellen des Azure Data Explorer-Clusters

1. Erstellen Sie Ihren Cluster mit dem folgenden Befehl:

    ```azurecli-interactive
    az kusto cluster create --cluster-name azureclitest --sku name="Standard_D13_v2" tier="Standard" --resource-group testrg --location westus
    ```

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | name | *azureclitest* | Der gewünschte Name Ihres Clusters.|
   | sku | *Standard_D13_v2* | Die SKU, die für Ihren Cluster verwendet wird. Parameter: *name*: Der SKU-Name *tier*: Die SKU-Ebene |
   | resource-group | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |
   | location | *westus* | Der Standort, an dem der Cluster erstellt wird. |

    Sie können auch noch weitere optionale Parameter verwenden, etwa die Kapazität des Clusters.

1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Cluster erfolgreich erstellt wurde:

    ```azurecli-interactive
    az kusto cluster show --cluster-name azureclitest --resource-group testrg
    ```

Wenn das Ergebnis `provisioningState` mit dem Wert `Succeeded` enthält, wurde der Cluster erfolgreich erstellt.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Erstellen der Datenbank im Azure Data Explorer-Cluster

1. Erstellen Sie Ihre Datenbank mit dem folgenden Befehl:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --database-name clidatabase --resource-group testrg --read-write-database soft-delete-period=P365D hot-cache-period=P31D location=westus
    ```

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | cluster-name | *azureclitest* | Der Name Ihres Clusters, in dem die Datenbank erstellt werden soll.|
   | database-name | *clidatabase* | Der Name Ihrer Datenbank.|
   | resource-group | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |
   | read-write-database | *P365D* *P31D* *westus* | Der Datenbanktyp. Parameter: *soft-delete-period*: Gibt den Zeitraum an, wie lange Daten für Abfragen verfügbar sein sollen. Weitere Informationen finden Sie unter [Aufbewahrungsrichtlinie](kusto/management/retentionpolicy.md). *hot-cache-period*: Gibt den Zeitraum an, wie lange Daten im Cache verfügbar sein sollen. Weitere Informationen finden Sie unter [Cacherichtlinie](kusto/management/cachepolicy.md). *location*: Der Standort, an dem die Datenbank erstellt wird. |

1. Führen Sie den folgenden Befehl aus, um die erstellte Datenbank anzuzeigen:

    ```azurecli-interactive
    az kusto database show --database-name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Jetzt verfügen Sie über einen Cluster und eine Datenbank.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

* Wenn Sie unsere anderen Artikel durcharbeiten möchten, behalten Sie die erstellten Ressourcen bei.
* Löschen Sie den Cluster, um Ressourcen zu bereinigen. Wenn Sie einen Cluster löschen, werden auch alle darin enthaltenen Datenbanken gelöscht. Verwenden Sie den folgenden Befehl, um Ihren Cluster zu löschen:

    ```azurecli-interactive
    az kusto cluster delete --cluster-name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Python](python-ingest-data.md)
