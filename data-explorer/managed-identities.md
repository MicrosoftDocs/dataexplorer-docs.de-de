---
title: Konfigurieren verwalteter Identitäten für Azure Data Explorer-Cluster
description: Hier erfahren Sie, wie Sie verwaltete Identitäten für Azure Data Explorer-Cluster konfigurieren.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 59392c8ff555df99ccb7c4a5b7503266cb095552
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515836"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Konfigurieren verwalteter Identitäten für Ihren Azure Data Explorer-Cluster

Durch eine [verwaltete Entität aus Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) kann Ihr Cluster problemlos auf andere durch AAD geschützte Ressourcen wie Azure Key Vault zugreifen. Da die Identität von der Azure-Plattform verwaltet wird, müssen Sie keine Geheimnisse bereitstellen oder rotieren. In diesem Artikel erfahren Sie, wie Sie eine verwaltete Identität für Azure Data Explorer-Cluster erstellen. Die Konfiguration der verwalteten Identität wird zurzeit nur für die [Aktivierung von kundenseitig verwalteten Schlüsseln für Ihren Cluster](security.md#customer-managed-keys-with-azure-key-vault) unterstützt.

> [!Note]
> Verwaltete Identitäten für Azure Data Explorer verhalten sich nicht wie erwartet, wenn Ihr Azure Data Explorer-Cluster abonnement- oder mandantenübergreifend migriert wird. Die App muss eine neue Identität abrufen. Zu diesem Zweck kann die Funktion [deaktiviert](#disable-a-system-assigned-identity) und dann [erneut aktiviert](#add-a-system-assigned-identity) werden. Darüber hinaus müssen auch Zugriffsrichtlinien nachgeschalteter Ressourcen aktualisiert werden, um die neue Identität zu verwenden.

## <a name="add-a-system-assigned-identity"></a>Hinzufügen einer systemseitig zugewiesenen Identität
                                                                                                    
Weisen Sie eine vom System zugewiesene Identität zu, die an Ihren Cluster gebunden ist und gelöscht wird, wenn Ihr Cluster gelöscht wird. Ein Cluster kann nur über eine einzelne systemseitig zugewiesene Identität verfügen. Für die Erstellung eines Clusters mit einer systemseitig zugewiesenen Identität muss eine zusätzliche Eigenschaft für den Cluster festgelegt werden. Die systemseitig zugewiesene Identität wird mithilfe von C#, ARM-Vorlagen oder Azure-Portal wie unten beschrieben hinzugefügt.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Hinzufügen einer systemseitig zugewiesenen Identität im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

#### <a name="new-azure-data-explorer-cluster"></a>Neuer Azure Data Explorer-Cluster

1. [Erstellen eines Azure Data Explorer-Clusters](create-cluster-database-portal.md#create-a-cluster) 
1. Wählen Sie auf der Registerkarte **Sicherheit** für **Systemseitig zugewiesene Identität** die Option **Ein** aus. Um die systemseitig zugewiesene Identität zu entfernen, wählen Sie **Aus** aus.
2. Wählen Sie **Weiter:Tags>** oder **Überprüfen und erstellen** aus, um den Cluster zu erstellen.

    ![Hinzufügen einer systemseitig zugewiesenen Identität zum neuen Cluster](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Vorhandener Azure Data Explorer-Cluster

1. Öffnen Sie einen vorhandenen Azure Data Explorer-Cluster.
1. Wählen Sie im linken Bereich des Portals **Einstellungen** > **Identität** aus.
1. Gehen Sie im Bereich **Identität** auf der Registerkarte **Vom System zugewiesen** so vor:
   1. Schieben Sie den Schieberegler **Status** auf **Ein**.
   1. Wählen Sie **Speichern** aus.
   1. Wählen Sie im Popupfenster **Ja** aus.

    ![Hinzufügen einer systemseitig zugewiesenen Identität](media/managed-identities/turn-system-assigned-identity-on.png)

1. Nach einigen Minuten wird auf dem Bildschirm Folgendes angezeigt: 
  * **Objekt-ID**: wird für von Kunden verwaltete Schlüssel verwendet. 
  * **Rollenzuweisungen**: Klicken Sie auf den Link, um relevante Rollen zuzuweisen.

    ![Systemseitig zugewiesene Identität EIN](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>Hinzufügen einer systemseitig zugewiesenen Identität unter Verwendung von C#

#### <a name="prerequisites"></a>Voraussetzungen

Gehen Sie wie folgt vor, um eine verwaltete Identität mithilfe des C#-Clients für Azure Data Explorer einzurichten:

* Installieren Sie das [Azure Data Explorer-NuGet-Paket (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installieren Sie das [NuGet-Paket Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) für die Authentifizierung.
* [Erstellen Sie eine Azure AD-Anwendung](/azure/active-directory/develop/howto-create-service-principal-portal) und einen Dienstprinzipal, der auf Ressourcen zugreifen kann. Sie fügen die Rollenzuweisung im Abonnementumfang hinzu und erhalten die erforderlichen Werte für `Directory (tenant) ID`, `Application ID` und `Client Secret`.

#### <a name="create-or-update-your-cluster"></a>Erstellen oder Aktualisieren Ihres Clusters

1. Erstellen oder aktualisieren Sie Ihren Cluster mithilfe der Eigenschaft `Identity`:

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
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Cluster erfolgreich mit einer Identität erstellt oder aktualisiert wurde:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Wenn das Ergebnis `ProvisioningState` mit dem Wert `Succeeded` enthält, wurde der Cluster erstellt oder aktualisiert und sollte über folgende Eigenschaften verfügen:

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId` und `TenantId` werden durch GUIDs ersetzt. Die Eigenschaft `TenantId` gibt den AAD-Mandanten an, zu dem die Identität gehört. `PrincipalId` ist ein eindeutiger Bezeichner für die neue Identität des Clusters. In AAD weist der Dienstprinzipal denselben Namen auf, den Sie für Ihre App Service- oder Azure Functions-Instanz vergeben haben.

# <a name="arm-template"></a>[ARM-Vorlage](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Hinzufügen einer systemseitig zugewiesenen Identität unter Verwendung einer Azure Resource Manager-Vorlage

Mithilfe einer Azure Resource Manager-Vorlage kann die Bereitstellung Ihrer Azure-Ressourcen automatisiert werden. Weitere Informationen zum Bereitstellen in Azure Data Explorer finden Sie unter [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mithilfe einer Azure Resource Manager-Vorlage](create-cluster-database-resource-manager.md).

Durch Hinzufügen des systemseitig zugewiesenen Typs wird Azure angewiesen, die Identität für Ihrem Cluster zu erstellen und zu verwalten. Ressourcen vom Typ `Microsoft.Kusto/clusters` können mit einer Identität erstellt werden, indem die folgende Eigenschaft in der Ressourcendefinition eingeschlossen wird: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Beispiel:

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

Wenn der Cluster erstellt wird, verfügt er über folgende zusätzliche Eigenschaften:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` und `<PRINCIPALID>` werden durch GUIDs ersetzt. Die Eigenschaft `TenantId` gibt den AAD-Mandanten an, zu dem die Identität gehört. `PrincipalId` ist ein eindeutiger Bezeichner für die neue Identität des Clusters. In AAD weist der Dienstprinzipal denselben Namen auf, den Sie für Ihre App Service- oder Azure Functions-Instanz vergeben haben.

---

## <a name="disable-a-system-assigned-identity"></a>Deaktivieren einer systemseitig zugewiesenen Identität

Wenn Sie eine systemseitig zugewiesene Identität entfernen, wird sie auch aus AAD gelöscht. Systemseitig zugewiesene Identitäten werden außerdem automatisch aus AAD entfernt, wenn die Clusterressource gelöscht wird. Eine systemseitig zugewiesene Identität kann durch Deaktivieren des Features entfernt werden.  Die systemseitig zugewiesene Identität wird mithilfe von C#, ARM-Vorlagen oder des Azure-Portals wie unten beschrieben entfernt.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>Deaktivieren einer systemseitig zugewiesenen Identität im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie im linken Bereich des Portals **Einstellungen** > **Identität** aus.
1. Gehen Sie im Bereich **Identität** auf der Registerkarte **Vom System zugewiesen** so vor:
    1. Schieben Sie den Schieberegler **Status** auf **Aus**.
    1. Wählen Sie **Speichern** aus.
    1. Wählen Sie im Popupfenster **Ja** aus, um die systemseitig zugewiesene Identität zu deaktivieren. Der Bereich **Identität** wechselt wieder in den gleichen Zustand wie vor Hinzufügen der systemseitig zugewiesenen Identität.

    ![Systemseitig zugewiesene Identität AUS](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>Entfernen einer systemseitig zugewiesenen Identität mithilfe von C#

Führen Sie Folgendes aus, um die systemseitig zugewiesene Identität zu entfernen:

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[ARM-Vorlage](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Entfernen einer systemseitig zugewiesenen Identität mithilfe einer Azure Resource Manager-Vorlage

Führen Sie Folgendes aus, um die systemseitig zugewiesene Identität zu entfernen:

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>Nächste Schritte

* [Schützen von Azure Data Explorer-Clustern in Azure](security.md)
* [Sichern Ihres Clusters in Azure Data Explorer mithilfe von Disk Encryption (Azure-Portal)](cluster-disk-encryption.md) durch Aktivieren der Verschlüsselung ruhender Daten
 * [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe von C#](customer-managed-keys-csharp.md)
 * [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe der Azure Resource Manager-Vorlage](customer-managed-keys-resource-manager.md)
