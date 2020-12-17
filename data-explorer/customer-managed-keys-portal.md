---
title: Konfigurieren von kundenseitig verwalteten Schlüsseln über das Azure-Portal
description: Es wird beschrieben, wie Sie kundenseitig verwaltete Schlüssel für die Verschlüsselung von Azure Data Explorer-Daten mit dem Azure-Portal konfigurieren.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 0f77782b5174683d091685064afa7debff7ae777
ms.sourcegitcommit: 202357f866801aafd92e3e29a84bb312e17aebc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96933906"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Konfigurieren von kundenseitig verwalteten Schlüsseln über das Azure-Portal

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-Vorlage](customer-managed-keys-resource-manager.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Aktivieren der Verschlüsselung mit von Kunden verwalteten Schlüsseln im Azure-Portal

In diesem Artikel erfahren Sie, wie Sie die Verschlüsselung mit vom Kunden verwalteten Schlüsseln über das Azure-Portal aktivieren können. Die Azure Data Explorer-Verschlüsselung verwendet standardmäßig von Microsoft verwaltete Schlüssel. Konfigurieren Sie Ihren Azure Data Explorer-Cluster für die Verwendung von kundenseitig verwalteten Schlüsseln, und geben Sie den Schlüssel an, der dem Cluster zugeordnet werden soll.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer [Azure Data Explorer-Clusterressource](create-cluster-database-portal.md#create-a-cluster).
1. Wählen Sie im linken Bereich des Portals **Einstellungen** > **Verschlüsselung** aus.
1. Wählen Sie im Bereich **Verschlüsselung** die Option **Ein** für die Einstellung **Kundenseitig verwalteter Schlüssel** aus.
1. Klicken Sie auf **Schlüssel auswählen**.

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-encryption-setting.png" alt-text="Konfigurieren von kundenseitig verwalteten Schlüsseln":::

1. Wählen Sie im Fenster **Schlüssel aus Azure Key Vault auswählen** einen vorhandenen **Schlüsseltresor** aus der Dropdownliste aus. Wenn Sie **Neu erstellen** auswählen, um einen [neuen Schlüsseltresor zu erstellen](/azure/key-vault/quick-create-portal#create-a-vault), werden Sie zum Bildschirm **Schlüsseltresor erstellen** weitergeleitet.

1. Wählen Sie **Schlüssel** aus.
1. Wählen Sie **Version** aus.
1. Klicken Sie auf **Auswählen**.

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-key-vault.png" alt-text="Auswählen des Schlüssels aus Azure Key Vault":::

1. Wählen Sie unter **Identitätstyp** die Option **Systemseitig zugewiesen** oder **Benutzerseitig zugewiesene** aus.
1. Wählen Sie bei der Auswahl von **Benutzerseitig zugewiesene** in der Dropdownliste eine benutzerseitig zugewiesene Identität aus.

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-select-user-type.png" alt-text="Auswählen des Typs „Verwaltete Identität“":::

1. Wählen Sie im Bereich **Verschlüsselung**, der jetzt Ihren Schlüssel enthält, **Speichern** aus. Wenn die CMK-Erstellung erfolgreich ist, wird eine Erfolgsmeldung in **Benachrichtigungen** angezeigt.

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-before-save.png" alt-text="Speichern des kundenseitig verwalteten Schlüssels":::

Wenn Sie bei der Aktivierung von kundenseitig verwalteten Schlüsseln für Ihren Azure Data Explorer-Cluster eine systemseitig zugewiesene Identität auswählen, erstellen Sie eine systemseitig zugewiesene Identität für den Cluster, falls diese noch nicht vorhanden ist. Darüber hinaus stellen Sie die erforderlichen get-, wrapKey- und unwrapKey-Berechtigungen für Ihren Azure Data Explorer-Cluster im ausgewählten Key Vault zur Verfügung und erhalten die Key Vault-Eigenschaften.

> [!NOTE]
> Wählen Sie **Aus**, um den vom Kunden verwalteten Schlüssel nach seiner Erstellung zu entfernen.

## <a name="next-steps"></a>Nächste Schritte

* [Schützen von Azure Data Explorer-Clustern in Azure](security.md)
* [Sichern Ihres Clusters in Azure Data Explorer mithilfe von Disk Encryption (Azure-Portal)](cluster-disk-encryption.md) durch Aktivieren der Verschlüsselung ruhender Daten
* [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe der Azure Resource Manager-Vorlage](customer-managed-keys-resource-manager.md)
* [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe von C#](customer-managed-keys-csharp.md)
