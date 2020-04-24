---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492838"
---
Azure Data Explorer verschlüsselt alle Daten in einem Speicherkonto für ruhende Daten. Standardmäßig werden Daten mit von Microsoft verwalteten Schlüsseln verschlüsselt. Um zusätzliche Kontrolle über die Verschlüsselungsschlüssel zu erhalten, können Sie kundenseitig verwaltete Schlüssel für die Datenverschlüsselung bereitstellen. 

Kundenseitig verwaltete Schlüssel müssen in [Azure Key Vault](/azure/key-vault/key-vault-overview) gespeichert werden. Sie können eigene Schlüssel erstellen und in einer Key Vault-Instanz speichern oder eine Azure Key Vault-API verwenden, um Schlüssel zu generieren. Der Azure Data Explorer-Cluster und die Key Vault-Instanz müssen sich in der gleichen Region befinden, können aber zu verschiedenen Abonnements gehören. Eine ausführliche Erläuterung zu kundenseitig verwalteten Schlüsseln finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](/azure/storage/common/storage-service-encryption) im Abschnitt „Von Kunden verwaltete Schlüssel mit Azure Key Vault“. 

Der vorliegende Artikel veranschaulicht das Konfigurieren von kundenseitig verwalteten Schlüsseln.

## <a name="configure-azure-key-vault"></a>Konfigurieren von Azure Key Vault

Um kundenseitig verwaltete Schlüssel mit Azure Data Explorer zu konfigurieren, müssen Sie [zwei Eigenschaften in Key Vault festlegen](/azure/key-vault/key-vault-ovw-soft-delete): **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen). Diese Eigenschaften sind standardmäßig nicht aktiviert. Um diese Eigenschaften zu aktivieren, führen Sie **Aktivieren des vorläufigen Löschens** und **Aktivieren des Bereinigungsschutzes** in [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) oder [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) in einem neuen oder vorhandenen Schlüsseltresor aus. Es werden ausschließlich RSA-Schlüssel und die Größe 2.048 unterstützt. Weitere Informationen zu Schlüsseln finden Sie unter [Key Vault-Schlüssel](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Datenverschlüsselung mithilfe von kundenseitig verwalteten Schlüsseln wird auf [Leader- und Follower-Clustern](/azure/data-explorer/follower) nicht unterstützt. 

## <a name="assign-an-identity-to-the-cluster"></a>Zuweisen einer Identität zum Cluster

Um kundenseitig verwaltete Schlüssel für Ihren Cluster zu aktivieren, weisen Sie dem Cluster zunächst eine vom System zugewiesene verwaltete Identität hinzu. Sie verwenden diese verwaltete Identität, um dem Cluster Berechtigungen für den Zugriff auf die Key Vault-Instanz zu gewähren. Informationen zum Konfigurieren systemseitig zugewiesener verwalteter Identitäten finden Sie unter [Verwaltete Identitäten](/azure/data-explorer/managed-identities).