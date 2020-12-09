---
title: Konfigurieren verwalteter Identitäten für Azure Data Explorer-Cluster
description: Hier erfahren Sie, wie Sie verwaltete Identitäten für Azure Data Explorer-Cluster konfigurieren.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 2bfc2cd69d88395e04af16e732564fb90170ee7f
ms.sourcegitcommit: 7edce9d9d20f9c0505abda67bb8cc3d2ecd60d15
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96524282"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Konfigurieren verwalteter Identitäten für Ihren Azure Data Explorer-Cluster

Durch eine [verwaltete Entität aus Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) kann Ihr Cluster problemlos auf andere durch Azure AD geschützte Ressourcen wie Azure Key Vault zugreifen. Da die Identität von der Azure-Plattform verwaltet wird, müssen Sie keine Geheimnisse bereitstellen oder rotieren. Die Konfiguration der verwalteten Identität wird zurzeit nur für die [Aktivierung von kundenseitig verwalteten Schlüsseln für Ihren Cluster](security.md#customer-managed-keys-with-azure-key-vault) unterstützt.

Ihrem Azure Data Explorer-Cluster können zwei Arten von Identitäten zugewiesen werden:

* **Systemseitig zugewiesene Identität:** Diese Identität ist an Ihren Cluster gebunden und wird gelöscht, wenn die Ressource gelöscht wird. Ein Cluster kann nur über eine einzelne systemseitig zugewiesene Identität verfügen.
* **Benutzerseitig zugewiesene Identität:** Hierbei handelt es sich um eine eigenständige Azure-Ressource, die Ihrem Cluster zugewiesen werden kann. Ein Cluster kann über mehrere benutzerseitig zugewiesene Identitäten verfügen.

In diesem Artikel erfahren Sie, wie Sie systemseitig zugewiesene und benutzerseitig zugewiesene verwaltete Identitäten für Azure Data Explorer-Cluster hinzufügen und entfernen.

> [!Note]
> Verwaltete Identitäten für Azure Data Explorer verhalten sich nicht wie erwartet, wenn Ihr Azure Data Explorer-Cluster abonnement- oder mandantenübergreifend migriert wird. Die App muss eine neue Identität abrufen. Zu diesem Zweck kann die Funktion [deaktiviert](#remove-a-system-assigned-identity) und dann [erneut aktiviert](#add-a-system-assigned-identity) werden. Darüber hinaus müssen auch Zugriffsrichtlinien nachgeschalteter Ressourcen aktualisiert werden, um die neue Identität zu verwenden.

## <a name="add-a-system-assigned-identity"></a>Hinzufügen einer systemseitig zugewiesenen Identität

Weisen Sie eine vom System zugewiesene Identität zu, die an Ihren Cluster gebunden ist und gelöscht wird, wenn Ihr Cluster gelöscht wird. Ein Cluster kann nur über eine einzelne systemseitig zugewiesene Identität verfügen. Für die Erstellung eines Clusters mit einer systemseitig zugewiesenen Identität muss eine zusätzliche Eigenschaft für den Cluster festgelegt werden. Fügen Sie die systemseitig zugewiesene Identität mithilfe des Azure-Portals, von C# oder einer Resource Manager-Vorlage hinzu, wie nachfolgend beschrieben.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Hinzufügen einer systemseitig zugewiesenen Identität im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

#### <a name="new-azure-data-explorer-cluster"></a>Neuer Azure Data Explorer-Cluster

1. [Erstellen eines Azure Data Explorer-Clusters](create-cluster-database-portal.md#create-a-cluster) 
1. Wählen Sie auf der Registerkarte **Sicherheit** für **Systemseitig zugewiesene Identität** die Option **Ein** aus. Um die systemseitig zugewiesene Identität zu entfernen, wählen Sie **Aus** aus.
1. Wählen Sie **Weiter: Tags >** oder **Überprüfen und erstellen** aus, um den Cluster zu erstellen.

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
    * **Objekt-ID**: Wird für kundenseitig verwaltete Schlüssel verwendet.
    * **Berechtigungen**: Wählen Sie die entsprechenden Rollenzuweisungen aus.

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

`PrincipalId` und `TenantId` werden durch GUIDs ersetzt. Die Eigenschaft `TenantId` gibt den Azure AD-Mandanten an, zu dem die Identität gehört. `PrincipalId` ist ein eindeutiger Bezeichner für die neue Identität des Clusters. In Azure AD weist der Dienstprinzipal den gleichen Namen auf, den Sie für Ihre App Service- oder Azure Functions-Instanz vergeben haben.

# <a name="resource-manager-template"></a>[Resource Manager: Vorlage](#tab/arm)

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
    }
}
```

> [!NOTE]
> Ein Cluster kann gleichzeitig sowohl über systemseitig als auch über benutzerseitig zugewiesene Identitäten verfügen. Die Eigenschaft `type` hätte dann den Wert `SystemAssigned,UserAssigned`.

Wenn der Cluster erstellt wird, verfügt er über folgende zusätzliche Eigenschaften:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` und `<PRINCIPALID>` werden durch GUIDs ersetzt. Die Eigenschaft `TenantId` gibt den Azure AD-Mandanten an, zu dem die Identität gehört. `PrincipalId` ist ein eindeutiger Bezeichner für die neue Identität des Clusters. In Azure AD weist der Dienstprinzipal den gleichen Namen auf, den Sie für Ihre App Service- oder Azure Functions-Instanz vergeben haben.

---

## <a name="remove-a-system-assigned-identity"></a>Entfernen einer systemseitig zugewiesenen Identität

Wenn Sie eine systemseitig zugewiesene Identität entfernen, wird sie auch aus Azure AD gelöscht. Systemseitig zugewiesene Identitäten werden außerdem automatisch aus Azure AD entfernt, wenn die Clusterressource gelöscht wird. Eine systemseitig zugewiesene Identität kann durch Deaktivieren des Features entfernt werden. Entfernen Sie die systemseitig zugewiesene Identität mithilfe des Azure-Portals, von C# oder einer Resource Manager-Vorlage, wie nachfolgend beschrieben.

# <a name="azure-portal"></a>[Azure-Portal](#tab/portal)

### <a name="remove-a-system-assigned-identity-using-the-azure-portal"></a>Entfernen einer systemseitig zugewiesenen Identität im Azure-Portal

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

# <a name="resource-manager-template"></a>[Resource Manager: Vorlage](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Entfernen einer systemseitig zugewiesenen Identität mithilfe einer Azure Resource Manager-Vorlage

Führen Sie Folgendes aus, um die systemseitig zugewiesene Identität zu entfernen:

```json
"identity": {
    "type": "None"
}
```

> [!NOTE]
> Wenn der Cluster gleichzeitig systemseitig und benutzerseitig zugewiesene Identitäten enthält, lautet die Eigenschaft `type` nach dem Entfernen der systemseitig zugewiesenen Identität `UserAssigned`.

---

## <a name="add-a-user-assigned-identity"></a>Hinzufügen einer benutzerseitig zugewiesenen Identität

Weisen Sie Ihrem Cluster eine benutzerseitig zugewiesene verwaltete Identität zu. Ein Cluster kann über mehrere benutzerseitig zugewiesene Identitäten verfügen. Für die Erstellung eines Clusters mit einer benutzerseitig zugewiesenen Identität muss eine zusätzliche Eigenschaft für den Cluster festgelegt werden. Fügen Sie die benutzerseitig zugewiesene Identität mithilfe des Azure-Portals, von C# oder einer Resource Manager-Vorlage hinzu, wie nachfolgend beschrieben.

# <a name="azure-portal"></a>[Azure-Portal](#tab/portal)

### <a name="add-a-user-assigned-identity-using-the-azure-portal"></a>Hinzufügen einer benutzerseitig zugewiesenen Identität im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. [Erstellen Sie eine benutzerseitig verwaltete Identitätsressource.](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal#create-a-user-assigned-managed-identity)
1. Öffnen Sie einen vorhandenen Azure Data Explorer-Cluster.
1. Wählen Sie im linken Bereich des Portals **Einstellungen** > **Identität** aus.
1. Wählen Sie auf der Registerkarte **Benutzerseitig zugewiesen** die Option **Hinzufügen** aus.
1. Suchen Sie nach der zuvor erstellten Identität, und wählen Sie sie aus. Wählen Sie **Hinzufügen** aus.

    ![Hinzufügen einer benutzerseitig zugewiesenen Identität](media/managed-identities/user-assigned-identity-select.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-user-assigned-identity-using-c"></a>Hinzufügen einer benutzerseitig zugewiesenen Identität mithilfe von C#

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
    var identityName = "myIdentity";
    var userIdentityResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}";
    var userAssignedIdentities = new Dictionary<string, IdentityUserAssignedIdentitiesValue>(1) { { userIdentityResourceId, new IdentityUserAssignedIdentitiesValue() } };
    var identity = new Identity(type: IdentityType.UserAssigned, userAssignedIdentities: userAssignedIdentities);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

2. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Cluster erfolgreich mit einer Identität erstellt oder aktualisiert wurde:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Wenn das Ergebnis `ProvisioningState` mit dem Wert `Succeeded` enthält, wurde der Cluster erstellt oder aktualisiert und sollte über folgende Eigenschaften verfügen:

    ```csharp
    var userIdentity = cluster.Identity.UserAssignedIdentities[userIdentityResourceId];
    var principalId = userIdentity.PrincipalId;
    var clientId = userIdentity.ClientId;
    ```

`PrincipalId` ist ein eindeutiger Bezeichner für die Identität, der für die Azure AD-Verwaltung verwendet wird. `ClientId` ist ein eindeutiger Bezeichner für die neue Identität der Anwendung, der bei Runtimeaufrufen angibt, welche Identität verwendet werden soll.

# <a name="resource-manager-template"></a>[Resource Manager: Vorlage](#tab/arm)

### <a name="add-a-user-assigned-identity-using-an-azure-resource-manager-template"></a>Hinzufügen einer benutzerseitig zugewiesenen Identität unter Verwendung einer Azure Resource Manager-Vorlage

Mithilfe einer Azure Resource Manager-Vorlage kann die Bereitstellung Ihrer Azure-Ressourcen automatisiert werden. Weitere Informationen zum Bereitstellen in Azure Data Explorer finden Sie unter [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mithilfe einer Azure Resource Manager-Vorlage](create-cluster-database-resource-manager.md).

Ressourcen vom Typ `Microsoft.Kusto/clusters` können mit einer benutzerseitig zugewiesenen Identität erstellt werden, indem die folgende Eigenschaft in die Ressourcendefinition eingeschlossen wird. Ersetzen Sie hierbei `<RESOURCEID>` durch die Ressourcen-ID der gewünschten Identität:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
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
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Wenn der Cluster erstellt wird, verfügt er über folgende zusätzliche Eigenschaften:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

`PrincipalId` ist ein eindeutiger Bezeichner für die Identität, der für die Azure AD-Verwaltung verwendet wird. `ClientId` ist ein eindeutiger Bezeichner für die neue Identität der Anwendung, der bei Runtimeaufrufen angibt, welche Identität verwendet werden soll.

> [!NOTE]
> Ein Cluster kann gleichzeitig sowohl über systemseitig als auch über benutzerseitig zugewiesene Identitäten verfügen. In diesem Fall hat die `type`-Eigenschaft den Wert `SystemAssigned,UserAssigned`.

---

## <a name="remove-a-user-assigned-managed-identity-from-a-cluster"></a>Entfernen einer benutzerseitig zugewiesenen verwalteten Identität aus einem Cluster

Entfernen Sie die benutzerseitig zugewiesene Identität mithilfe des Azure-Portals, von C# oder einer Resource Manager-Vorlage, wie nachfolgend beschrieben.

# <a name="azure-portal"></a>[Azure-Portal](#tab/portal)

### <a name="remove-a-user-assigned-managed-identity-using-the-azure-portal"></a>Entfernen einer benutzerseitig zugewiesenen verwalteten Identität über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie im linken Bereich des Portals **Einstellungen** > **Identität** aus.
1. Wählen Sie die Registerkarte **Benutzerseitig zugewiesen** aus.
1. Suchen Sie nach der zuvor erstellten Identität, und wählen Sie sie aus. Wählen Sie **Entfernen**.

    ![Entfernen einer benutzerseitig zugewiesenen Identität](media/managed-identities/user-assigned-identity-remove.png)

1. Wählen Sie im Popupfenster **Ja** aus, um die benutzerseitig zugewiesene Identität zu entfernen. Der Bereich **Identität** wechselt wieder in den gleichen Zustand wie vor dem Hinzufügen der benutzerseitig zugewiesenen Identität.

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-user-assigned-identity-using-c"></a>Entfernen einer benutzerseitig zugewiesenen Identität mithilfe von C#

Führen Sie Folgendes aus, um die benutzerseitig zugewiesene Identität zu entfernen:

```csharp
var identityName = "myIdentity";
var userIdentityResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}";
var userAssignedIdentities = new Dictionary<string, IdentityUserAssignedIdentitiesValue>(1) { { userIdentityResourceId, null } };
var identity = new Identity(type: IdentityType.UserAssigned, userAssignedIdentities: userAssignedIdentities);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="resource-manager-template"></a>[Resource Manager: Vorlage](#tab/arm)

### <a name="remove-a-user-assigned-identity-using-an-azure-resource-manager-template"></a>Entfernen einer benutzerseitig zugewiesenen Identität unter Verwendung einer Azure Resource Manager-Vorlage

Führen Sie Folgendes aus, um die benutzerseitig zugewiesene Identität zu entfernen:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": null
    }
}
```

> [!NOTE]
>
> * Legen Sie zum Entfernen von Identitäten deren Werte auf NULL fest. Dies wirkt sich nicht auf alle anderen vorhandenen Identitäten aus.
> * Wenn Sie alle benutzerseitig zugewiesenen Identitäten entfernen möchten, muss für die Eigenschaft `type` der Wert `None` festgelegt werden.
> * Wenn der Cluster gleichzeitig systemseitig und benutzerseitig zugewiesene Identitäten enthält, sind für die Eigenschaft `type` der Wert `SystemAssigned,UserAssigned` sowie die zu entfernenden Identitäten angegeben. Alternativ wird `SystemAssigned` festgelegt, um alle benutzerseitig zugewiesenen Identitäten zu entfernen.

---

## <a name="next-steps"></a>Nächste Schritte

* [Schützen von Azure Data Explorer-Clustern in Azure](security.md)
* [Sichern Ihres Clusters in Azure Data Explorer mithilfe von Disk Encryption (Azure-Portal)](cluster-disk-encryption.md) durch Aktivieren der Verschlüsselung ruhender Daten
* [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe von C#](customer-managed-keys-csharp.md)
* [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe der Azure Resource Manager-Vorlage](customer-managed-keys-resource-manager.md)
