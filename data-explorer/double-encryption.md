---
title: Aktivieren der Infrastrukturverschlüsselung (doppelte Verschlüsselung) während der Clustererstellung in Azure Data Explorer
description: In diesem Artikel wird beschrieben, wie Sie die Infrastrukturverschlüsselung (doppelte Verschlüsselung) während der Clustererstellung in Azure Data Explorer aktivieren.
author: orspod
ms.author: orspodek
ms.reviewer: toleibov
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/11/2020
ms.openlocfilehash: 4949190befdc8adcec9f8115305a2a403994395f
ms.sourcegitcommit: 88291fd9cebc26e5210463cb95be5540bf84eef8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92437403"
---
# <a name="enable-infrastructure-encryption-double-encryption-during-cluster-creation-in-azure-data-explorer"></a>Aktivieren der Infrastrukturverschlüsselung (doppelte Verschlüsselung) während der Clustererstellung in Azure Data Explorer
  
Wenn Sie einen Cluster erstellen, wird der zugehörige Speicher [automatisch auf Dienstebene verschlüsselt](/azure/storage/common/storage-service-encryption). Wenn Sie Ihre Daten noch besser schützen möchten, können Sie auch die [Azure Storage-Verschlüsselung auf Infrastrukturebene](/azure/storage/common/infrastructure-encryption-enable) aktivieren (auch als doppelte Verschlüsselung bezeichnet). Wenn die Infrastrukturverschlüsselung aktiviert ist, werden Daten im Speicherkonto zweimal – einmal auf Dienstebene und einmal auf Infrastrukturebene – mit zwei unterschiedlichen Verschlüsselungsalgorithmen und zwei verschiedenen Schlüsseln verschlüsselt. Die doppelte Verschlüsselung von Azure Storage-Daten schützt vor dem Szenario, dass einer der Verschlüsselungsalgorithmen oder Schlüssel kompromittiert wurde. In diesem Szenario werden die Daten weiterhin durch die zusätzliche Verschlüsselungsebene geschützt.

> [!IMPORTANT]
> * Das Aktivieren der doppelten Verschlüsselung ist nur während der Clustererstellung möglich.
> * Sobald die Infrastrukturverschlüsselung für Ihren Cluster aktiviert ist, können Sie sie **nicht** mehr deaktivieren.

# <a name="azure-portal"></a>[Azure-Portal](#tab/portal)

1. [Erstellen eines Azure Data Explorer-Clusters](create-cluster-database-portal.md#create-a-cluster) 
1. Wählen Sie auf der Registerkarte **Sicherheit** unter **Enable Double Encryption** (Doppelte Verschlüsselung aktivieren) die Option **Ein** aus. Wählen Sie zum Entfernen der doppelten Verschlüsselung **Aus** aus.
1. Wählen Sie **Weiter:Netzwerk>** oder **Überprüfen + erstellen** aus, um den Cluster zu erstellen.

    :::image type="content" source="media/double-encryption/double-encryption-portal.png" alt-text="Doppelte Verschlüsselung, neuer Cluster":::


# <a name="c"></a>[C#](#tab/c-sharp)

Sie können die Infrastrukturverschlüsselung während der Clustererstellung mithilfe von C# aktivieren.

## <a name="prerequisites"></a>Voraussetzungen

Richten Sie eine verwaltete Identität mithilfe des C#-Clients für Azure Data Explorer ein:

* Installieren Sie das [Azure Data Explorer-NuGet-Paket (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installieren Sie das [NuGet-Paket Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) für die Authentifizierung.
* [Erstellen Sie eine Azure AD-Anwendung](/azure/active-directory/develop/howto-create-service-principal-portal) und einen Dienstprinzipal, der auf Ressourcen zugreifen kann. Sie fügen die Rollenzuweisung im Abonnementumfang hinzu und erhalten die erforderlichen Werte für `Directory (tenant) ID`, `Application ID` und `Client Secret`.

## <a name="create-your-cluster"></a>Erstellen Ihres Clusters

1. Erstellen Sie den Cluster mithilfe der Eigenschaft `enableDoubleEncryption`:

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
    var location = "East US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var enableDoubleEncryption = true;
    var cluster = new Cluster(location, sku, enableDoubleEncryption: enableDoubleEncryption);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Cluster erfolgreich erstellt wurde:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Wenn das Ergebnis `ProvisioningState` mit dem Wert `Succeeded` enthält, wurde der Cluster erfolgreich erstellt.

# <a name="arm-template"></a>[ARM-Vorlage](#tab/arm)

Sie können die Infrastrukturverschlüsselung während der Clustererstellung mithilfe von Azure Resource Manager aktivieren.

Mithilfe einer Azure Resource Manager-Vorlage kann die Bereitstellung Ihrer Azure-Ressourcen automatisiert werden. Weitere Informationen zum Bereitstellen in Azure Data Explorer finden Sie unter [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mithilfe einer Azure Resource Manager-Vorlage](create-cluster-database-resource-manager.md).

## <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Hinzufügen einer systemseitig zugewiesenen Identität unter Verwendung einer Azure Resource Manager-Vorlage

1. Fügen Sie den Typ „EnableDoubleEncryption“ hinzu, um Azure anzuweisen, die Infrastrukturverschlüsselung (doppelte Verschlüsselung) für Ihren Cluster zu aktivieren.
    
    ```json
    {
        "apiVersion": "2020-06-14",
        "type": "Microsoft.Kusto/clusters",
        "name": "[variables('clusterName')]",
        "location": "[resourceGroup().location]",
        "properties": {
            "trustedExternalTenants": [],
            "virtualNetworkConfiguration": null,
            "optimizedAutoscale": null,
            "enableDiskEncryption": false,
            "enableStreamingIngest": false,
            "enableDoubleEncryption": true,
        }
    }
    ```

1. Wenn der Cluster erstellt wird, verfügt er über folgende zusätzliche Eigenschaften:

    ```json
    "identity": {
        "type": "SystemAssigned",
        "tenantId": "<TENANTID>",
        "principalId": "<PRINCIPALID>"
    }
    ```
---

## <a name="next-steps"></a>Nächste Schritte

[Überprüfen der Clusterintegrität](check-cluster-health.md)
