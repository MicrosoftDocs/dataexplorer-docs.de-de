---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: c00ea9998b1ec6f05c36d0e0b926bd3b6f8d3509
ms.sourcegitcommit: 79d923d7b7e8370726974e67a984183905f323ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96933895"
---
Azure Data Explorer verschlüsselt alle Daten in einem Speicherkonto für ruhende Daten. Standardmäßig werden Daten mit von Microsoft verwalteten Schlüsseln verschlüsselt. Um zusätzliche Kontrolle über die Verschlüsselungsschlüssel zu erhalten, können Sie kundenseitig verwaltete Schlüssel für die Datenverschlüsselung bereitstellen. 

Kundenseitig verwaltete Schlüssel müssen in [Azure Key Vault](/azure/key-vault/key-vault-overview) gespeichert werden. Sie können eigene Schlüssel erstellen und in einer Key Vault-Instanz speichern oder eine Azure Key Vault-API verwenden, um Schlüssel zu generieren. Der Azure Data Explorer-Cluster und die Key Vault-Instanz müssen sich in der gleichen Region befinden, können aber zu verschiedenen Abonnements gehören. Eine ausführliche Erläuterung zu kundenseitig verwalteten Schlüsseln finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](/azure/storage/common/storage-service-encryption) im Abschnitt „Von Kunden verwaltete Schlüssel mit Azure Key Vault“. 

Der vorliegende Artikel veranschaulicht das Konfigurieren von kundenseitig verwalteten Schlüsseln.

## <a name="configure-azure-key-vault"></a>Konfigurieren von Azure Key Vault

Um kundenseitig verwaltete Schlüssel mit Azure Data Explorer zu konfigurieren, müssen Sie [zwei Eigenschaften in Key Vault festlegen](/azure/key-vault/key-vault-ovw-soft-delete): **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen). Diese Eigenschaften sind standardmäßig nicht aktiviert. Um diese Eigenschaften zu aktivieren, führen Sie **Aktivieren des vorläufigen Löschens** und **Aktivieren des Bereinigungsschutzes** in [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) oder [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) in einem neuen oder vorhandenen Schlüsseltresor aus. Es werden ausschließlich RSA-Schlüssel und die Größe 2.048 unterstützt. Weitere Informationen zu Schlüsseln finden Sie unter [Key Vault-Schlüssel](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Datenverschlüsselung mithilfe von kundenseitig verwalteten Schlüsseln wird auf [Leader- und Follower-Clustern](../follower.md) nicht unterstützt.

## <a name="assign-an-identity-to-the-cluster"></a>Zuweisen einer Identität zum Cluster

Um kundenseitig verwaltete Schlüssel für Ihren Cluster zu aktivieren, weisen Sie dem Cluster zunächst eine systemseitig oder benutzerseitig zugewiesene verwaltete Identität hinzu. Sie verwenden diese verwaltete Identität, um dem Cluster Berechtigungen für den Zugriff auf die Key Vault-Instanz zu gewähren. Informationen zum Konfigurieren verwalteter Identitäten finden Sie unter [Verwaltete Identitäten](../managed-identities.md).
