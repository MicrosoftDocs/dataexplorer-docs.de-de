---
title: Konfigurieren von kundenseitig verwalteten Schlüsseln in Azure Data Explorer mithilfe der Azure Resource Manager-Vorlage
description: In diesem Artikel wird beschrieben, wie Sie mit der Azure Resource Manager-Vorlage die Verschlüsselung von kundenseitig verwalteten Schlüsseln für Ihre Daten in Azure Data Explorer konfigurieren.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: c9594862d9dbe0eae1e8357a81e1bce5bba79da7
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515801"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe der Azure Resource Manager-Vorlage

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-Vorlage](customer-managed-keys-resource-manager.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurieren der Verschlüsselung mit von Kunden verwalteten Schlüsseln

In diesem Abschnitt konfigurieren Sie kundenseitig verwaltete Schlüssel mithilfe von Azure Resource Manager-Vorlagen. Die Azure Data Explorer-Verschlüsselung verwendet standardmäßig von Microsoft verwaltete Schlüssel. In diesem Schritt konfigurieren Sie Ihren Azure Data Explorer-Cluster für die Verwendung von kundenseitig verwalteten Schlüsseln und geben den Schlüssel an, der dem Cluster zugeordnet werden soll.

Sie können die Azure Resource Manager-Vorlage über das Azure-Portal oder mithilfe von PowerShell bereitstellen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>Aktualisieren der Schlüsselversion

Wenn Sie eine neue Version eines Schlüssels erstellen, müssen Sie den Cluster aktualisieren, damit er die neue Version verwendet. Rufen Sie zuerst `Get-AzKeyVaultKey` auf, um die aktuelle Version des Schlüssels abzurufen. Aktualisieren Sie dann die Schlüsseltresoreigenschaften des Clusters so, dass die neue Version des Schlüssels verwendet wird, wie unter [Konfigurieren der Verschlüsselung mit von Kunden verwalteten Schlüsseln](#configure-encryption-with-customer-managed-keys) gezeigt.

## <a name="next-steps"></a>Nächste Schritte

* [Schützen von Azure Data Explorer-Clustern in Azure](security.md)
* [Konfigurieren von verwalteten Identitäten für Ihren Azure Data Explorer-Cluster](managed-identities.md)
* [Sichern Ihres Clusters in Azure Data Explorer mithilfe von Disk Encryption (Azure-Portal)](cluster-disk-encryption.md) durch Aktivieren der Verschlüsselung ruhender Daten
* [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe von C#](customer-managed-keys-csharp.md)

