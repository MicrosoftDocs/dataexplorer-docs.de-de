---
title: Konfigurieren von kundenseitig verwalteten Schlüsseln mit der Azure-Befehlszeilenschnittstelle
description: In diesem Artikel wird beschrieben, wie Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln für Ihre Daten in Azure Data Explorer mithilfe der Azure-Befehlszeilenschnittstelle konfigurieren.
author: orspod
ms.author: orspodek
ms.reviewer: astauben
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/01/2020
ms.openlocfilehash: c343ac1177e439f75116a3da77def5862d5d2a7f
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872130"
---
# <a name="configure-customer-managed-keys-using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle zum Konfigurieren von kundenseitig verwalteten Schlüsseln

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-Vorlage](customer-managed-keys-resource-manager.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-using-azure-cli"></a>Aktivieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln mithilfe der Azure CLI
In diesem Artikel erfahren Sie, wie Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln über den Azure-Befehlszeilenschnittstellenclient aktivieren. Die Azure Data Explorer-Verschlüsselung verwendet standardmäßig von Microsoft verwaltete Schlüssel. Konfigurieren Sie Ihren Azure Data Explorer-Cluster für die Verwendung von kundenseitig verwalteten Schlüsseln, und geben Sie den Schlüssel an, der dem Cluster zugeordnet werden soll.

1. Führen Sie den folgenden Befehl aus, um sich bei Azure anzumelden:

    ```azurecli-interactive
    az login
    ```

1. Legen Sie das Abonnement fest, in dem der Cluster registriert ist. Ersetzen Sie *MyAzureSub* durch den Namen des gewünschten Azure-Abonnements.

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

1. Führen Sie den folgenden Befehl aus, um den neuen Schlüssel festzulegen.
    ```azurecli-interactive
    az kusto cluster update --cluster-name "mytestcluster" --resource-group "mytestrg" --key-vault-properties key-name="<key-name>" key-version="<key-version>" key-vault-uri="<key-vault-uri>"
    ```
1. Führen Sie den folgenden Befehl aus, und überprüfen Sie die keyVaultProperties-Eigenschaft, um sicherzustellen, dass der Cluster erfolgreich aktualisiert wurde.

    ```azurecli-interactive
    az kusto cluster show --cluster-name "mytestcluster" --resource-group "mytestrg"
    ```

