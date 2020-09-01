---
title: Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe von PowerShell
description: In diesem Artikel wird beschrieben, wie Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln für Ihre Daten in Azure Data Explorer mithilfe von PowerShell konfigurieren.
author: orspod
ms.author: orspodek
ms.reviewer: roshauli
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/04/2020
ms.openlocfilehash: 5c52253a6d8c4978931aeff3a5a5a73367f14ce5
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871994"
---
# <a name="configure-customer-managed-keys-using-powershell"></a>Konfigurieren kundenseitig verwalteter Schlüssel mit PowerShell

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-Vorlage](customer-managed-keys-resource-manager.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-using-powershell"></a>Aktivieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln mithilfe von PowerShell

In diesem Artikel erfahren Sie, wie Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln mithilfe von PowerShell aktivieren. Die Azure Data Explorer-Verschlüsselung verwendet standardmäßig von Microsoft verwaltete Schlüssel. Konfigurieren Sie Ihren Azure Data Explorer-Cluster für die Verwendung von kundenseitig verwalteten Schlüsseln, und geben Sie den Schlüssel an, der dem Cluster zugeordnet werden soll.

1. Führen Sie den folgenden Befehl aus, um sich bei Azure anzumelden:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Legen Sie das Abonnement fest, in dem der Cluster registriert ist.

    ```azurepowershell-interactive
    Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```

1. Führen Sie den folgenden Befehl aus, um den neuen Schlüssel festzulegen:

    ```azurepowershell-interactive
    Update-AzKustoCluster -ResourceGroupName "mytestrg" -Name "mytestcluster" -KeyVaultPropertyKeyName "<key-name>" -KeyVaultPropertyKeyVaultUri "<key-vault-uri>" -KeyVaultPropertyKeyVersion "<key-version>"
    ```

1. Führen Sie den folgenden Befehl aus, und überprüfen Sie die Eigenschaften vom Typ „KeyVaultProperty...“, um sicherzustellen, dass der Cluster erfolgreich aktualisiert wurde:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name "mytestcluster" -ResourceGroupName "mytestrg" | Format-List
    ```
