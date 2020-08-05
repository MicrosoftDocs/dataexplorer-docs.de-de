---
title: Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe von C#
description: In diesem Artikel wird beschrieben, wie Sie die Verschlüsselung von kundenseitig verwalteten Schlüsseln für Ihre Daten in Azure Data Explorer konfigurieren.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 5d06b226edd08cbe4696499f6a7ec206fc902608
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515788"
---
# <a name="configure-customer-managed-keys-using-c"></a>Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe von C#

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-Vorlage](customer-managed-keys-resource-manager.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurieren der Verschlüsselung mit von Kunden verwalteten Schlüsseln

In diesem Abschnitt wird gezeigt, wie Sie die Verschlüsselung von kundenseitig verwalteten Schlüsseln mithilfe des Azure Data Explorer-C#-Clients konfigurieren. 

### <a name="prerequisites"></a>Voraussetzungen

* Falls Sie Visual Studio 2019 nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2019 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**.

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

### <a name="install-c-nuget"></a>Installieren eines C#-NuGet-Pakets

* Installieren Sie das [Azure Data Explorer-NuGet-Paket (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Installieren Sie das [NuGet-Paket Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) für die Authentifizierung.

### <a name="authentication"></a>Authentifizierung

Zum Ausführen der Beispiele in diesem Artikel [erstellen Sie eine Azure AD-Anwendung](/azure/active-directory/develop/howto-create-service-principal-portal) und einen Dienstprinzipal, der auf Ressourcen zugreifen kann. Sie können eine Rollenzuweisung im Abonnementbereich hinzufügen und die erforderliche `Directory (tenant) ID`, `Application ID`und `Client Secret` abrufen.

### <a name="configure-cluster"></a>Konfigurieren des Clusters

Die Azure Data Explorer-Verschlüsselung verwendet standardmäßig von Microsoft verwaltete Schlüssel. Konfigurieren Sie Ihren Azure Data Explorer-Cluster für die Verwendung von kundenseitig verwalteten Schlüsseln, und geben Sie den Schlüssel an, der dem Cluster zugeordnet werden soll.

1. Aktualisieren Sie Ihren Cluster mit dem folgenden Code:

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
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Cluster erfolgreich aktualisiert wurde:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Wenn das Ergebnis `ProvisioningState` mit dem Wert `Succeeded` enthält, wurde Ihr Cluster erfolgreich aktualisiert.

## <a name="update-the-key-version"></a>Aktualisieren der Schlüsselversion

Wenn Sie eine neue Version eines Schlüssels erstellen, müssen Sie den Cluster aktualisieren, damit er die neue Version verwendet. Rufen Sie zuerst `Get-AzKeyVaultKey` auf, um die neueste Version des Schlüssels abzurufen. Aktualisieren Sie dann die Schlüsseltresoreigenschaften (Key Vault-Eigenschaften) des Clusters so, dass die neue Version des Schlüssels verwendet wird, wie in [Konfigurieren eines Clusters](#configure-cluster) gezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

* [Schützen von Azure Data Explorer-Clustern in Azure](security.md)
* [Konfigurieren von verwalteten Identitäten für Ihren Azure Data Explorer-Cluster](managed-identities.md)
* [Sichern Ihres Clusters in Azure Data Explorer mithilfe von Disk Encryption (Azure-Portal)](cluster-disk-encryption.md) durch Aktivieren der Verschlüsselung ruhender Daten
* [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe der Azure Resource Manager-Vorlage](customer-managed-keys-resource-manager.md)


