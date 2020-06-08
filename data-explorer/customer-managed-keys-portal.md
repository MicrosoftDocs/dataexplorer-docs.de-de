---
title: Konfigurieren von kundenseitig verwalteten Schlüsseln über das Azure-Portal
description: In diesem Artikel wird beschrieben, wie Sie die Verschlüsselung von kundenseitig verwalteten Schlüsseln für Ihre Daten in Azure Data Explorer konfigurieren.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: f5f44f427f21cdea4b0fea382c41df23925b3549
ms.sourcegitcommit: a7e040fc844098323aa1c00e254bcbcd41fe587f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84426568"
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

    ![Konfigurieren von kundenseitig verwalteten Schlüsseln](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. Wählen Sie im Fenster **Schlüssel aus Azure Key Vault auswählen** einen vorhandenen **Schlüsseltresor** aus der Dropdownliste aus. Wenn Sie **Neu erstellen** auswählen, um einen [neuen Schlüsseltresor zu erstellen](/azure/key-vault/quick-create-portal#create-a-vault), werden Sie zum Bildschirm **Schlüsseltresor erstellen** weitergeleitet.

1. Wählen Sie **Schlüssel** aus.
1. Wählen Sie **Version** aus.
1. Klicken Sie auf **Auswählen**.

    ![Auswählen des Schlüssels aus Azure Key Vault](media/customer-managed-keys-portal/cmk-key-vault.png)

1. Wählen Sie im Bereich **Verschlüsselung**, der jetzt Ihren Schlüssel enthält, **Speichern** aus. Wenn die CMK-Erstellung erfolgreich ist, wird eine Erfolgsmeldung in **Benachrichtigungen** angezeigt.

    ![Speichern des kundenseitig verwalteten Schlüssels](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Wenn Sie vom Kunden verwaltete Schlüssel für Ihren Azure Data Explorer-Cluster aktivieren, erstellen Sie eine dem System zugewiesene Identität für den Cluster, falls diese noch nicht vorhanden ist. Darüber hinaus stellen Sie die erforderlichen get-, wrapKey- und unwrapKey-Berechtigungen für Ihren Azure Data Explorer-Cluster im ausgewählten Key Vault zur Verfügung und erhalten die Key Vault-Eigenschaften. 

> [!NOTE]
> Wählen Sie **Aus**, um den vom Kunden verwalteten Schlüssel nach seiner Erstellung zu entfernen.

## <a name="next-steps"></a>Nächste Schritte

* [Schützen von Azure Data Explorer-Clustern in Azure](security.md)
* [Sichern Ihres Clusters in Azure Data Explorer – Azure-Portal](manage-cluster-security.md) durch Aktivieren der Verschlüsselung ruhender Daten
* [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe der Azure Resource Manager-Vorlage](customer-managed-keys-resource-manager.md)
* [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe von C#](customer-managed-keys-csharp.md)



