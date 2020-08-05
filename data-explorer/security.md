---
title: Schützen von Azure Data Explorer-Clustern in Azure
description: Hier erfahren Sie, wie Sie Cluster in Azure Data Explorer schützen.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 0330d0bf51e3bf8844e6545c9b57543274d56ed0
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515735"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Schützen von Azure Data Explorer-Clustern in Azure

Dieser Artikel enthält eine Einführung in die Sicherheit in Azure Data Explorer, um Sie beim Schutz Ihrer Daten und Ressourcen in der Cloud sowie bei der Erfüllung der Sicherheitsanforderungen Ihres Unternehmens zu unterstützen. Clusterschutz ist wichtig. Für den Schutz Ihrer Cluster stehen unter anderem Azure-Features für sicheren Zugriff und Speicher zur Verfügung. Dieser Artikel enthält hilfreiche Informationen zum Schutz Ihres Clusters.

## <a name="managed-identities-for-azure-resources"></a>Verwaltete Identitäten für Azure-Ressourcen

Eine gängige Herausforderung beim Erstellen von Cloudanwendungen ist die Verwaltung der Anmeldeinformationen im Code, die für die Authentifizierung bei Clouddiensten erforderlich sind. Der Schutz dieser Anmeldeinformationen ist eine wichtige Aufgabe. Die Anmeldeinformationen sollten nicht auf Entwicklerarbeitsstationen gespeichert oder in die Quellcodeverwaltung eingecheckt werden. Azure Key Vault bietet eine Möglichkeit zum sicheren Speichern von Anmeldeinformationen, Geheimnissen und anderen Schlüsseln. Um diese abrufen zu können, muss sich Ihr Code jedoch bei Key Vault authentifizieren.

Das Feature für verwaltete Identitäten für Azure-Ressourcen in Azure Active Directory (Azure AD) löst dieses Problem. Dieses Feature stellt für Azure-Dienste eine automatisch verwaltete Identität in Azure AD bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. Weitere Informationen zu diesem Dienst finden Sie auf der Übersichtsseite zu [verwalteten Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="data-encryption"></a>Datenverschlüsselung

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) unterstützt Sie beim Schutz Ihrer Daten gemäß den Sicherheits- und Complianceanforderungen Ihrer Organisation. Der Dienst bietet Volumeverschlüsselung für den Betriebssystemdatenträger und die regulären Datenträger der virtuellen Computer Ihres Clusters. Azure Disk Encryption ist außerdem mit [Azure Key Vault](/azure/key-vault/) verknüpft, um die Schlüssel und Geheimnisse für die Datenträgerverschlüsselung steuern und verwalten zu können. So wird sichergestellt, dass alle Daten auf den Datenträgern von virtuellen Computern verschlüsselt sind. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Von Kunden verwaltete Schlüssel mit Azure Key Vault

Standardmäßig werden Daten mit von Microsoft verwalteten Schlüsseln verschlüsselt. Für zusätzliche Kontrolle über die Verschlüsselungsschlüssel können Sie kundenseitig verwaltete Schlüssel für die Datenverschlüsselung bereitstellen. Sie können die Verschlüsselung Ihrer Daten auf der Speicherebene mit Ihren eigenen Schlüsseln verwalten. Ein kundenseitig verwalteter Schlüssel wird verwendet, um den Zugriff auf den Stammverschlüsselungsschlüssel zu schützen, der wiederum zur Ver- und Entschlüsselung sämtlicher Daten verwendet wird. Vom Kunden verwaltete Schlüssel bieten größere Flexibilität beim Erstellen, Rotieren, Deaktivieren und Widerrufen von Zugriffssteuerungen. Außerdem können Sie die zum Schutz Ihrer Daten verwendeten Verschlüsselungsschlüssel überwachen.

Verwenden Sie Azure Key Vault, um Ihre kundenseitig verwalteten Schlüssel zu speichern. Sie können Ihre eigenen Schlüssel erstellen und in einem Schlüsseltresor speichern oder eine Azure Key Vault-API verwenden, um Schlüssel zu generieren. Der Azure Data Explorer-Cluster und die Azure Key Vault-Instanz müssen sich in der gleichen Region befinden, können aber zu verschiedenen Abonnements gehören. Weitere Informationen zum Azure Key Vault finden Sie unter [What is Azure Key Vault? (Was ist der Azure Key Vault?)](/azure/key-vault/key-vault-overview). Eine ausführliche Erläuterung zu kundenseitig verwalteten Schlüsseln finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](/azure/storage/common/storage-service-encryption). Konfigurieren von kundenseitig verwalteten Schlüsseln in Ihrem Azure Data Explorer-Cluster mithilfe von [C#](customer-managed-keys-csharp.md) oder der [Azure Resource Manager-Vorlage](customer-managed-keys-resource-manager.md)

> [!Note]
> Von Kunden verwaltete Schlüssel basieren auf verwalteten Identitäten für Azure-Ressourcen, einem Feature von Azure Active Directory (Azure AD). Wenn Sie kundenseitig verwaltete Schlüssel im Azure-Portal konfigurieren möchten, müssen Sie für Ihren Cluster eine verwaltete Identität vom Typ **SystemAssigned** konfigurieren, wie in [Konfigurieren verwalteter Identitäten für Ihren Azure Data Explorer-Cluster](managed-identities.md) beschrieben.

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Speichern von kundenseitig verwalteten Schlüsseln in Azure Key Vault

Wenn Sie kundenseitig verwaltete Schlüssel für einen Cluster aktivieren möchten, speichern Sie Ihre Schlüssel in einer Azure Key Vault-Instanz. Sie müssen die Eigenschaften **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen) im Schlüsseltresor aktivieren. Der Schlüsseltresor muss sich im gleichen Abonnement befinden wie der Cluster. In Data Explorer werden verwaltete Identitäten für Azure-Ressourcen verwendet, um die Authentifizierung für den Schlüsseltresor für Verschlüsselungs- und Entschlüsselungsvorgänge durchzuführen. Verwaltete Identitäten unterstützen keine verzeichnisübergreifenden Szenarien.

#### <a name="rotate-customer-managed-keys"></a>Rotieren von kundenseitig verwalteten Schlüsseln

Sie können einen vom Kunden verwalteten Schlüssel in Azure Key Vault entsprechend Ihren Konformitätsrichtlinien rotieren. Wenn der Schlüssel rotiert wird, müssen Sie den Cluster aktualisieren, sodass der neue Schlüssel-URI verwendet wird. Durch Rotieren des Schlüssels werden die Daten im Cluster nicht erneut verschlüsselt. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Widerrufen des Zugriffs auf von Kunden verwaltete Schlüssel

Zum Widerrufen des Zugriffs auf von Kunden verwaltete Schlüssel können Sie PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden. Weitere Informationen finden Sie unter [Azure Key Vault – PowerShell](/powershell/module/az.keyvault/) oder [Azure Key Vault – CLI](/cli/azure/keyvault). Durch Widerrufen des Zugriffs wird der Zugriff auf alle Daten auf der Speicherebene des Clusters blockiert, da Azure Data Explorer dann nicht mehr auf den Verschlüsselungsschlüssel zugreifen kann.

> [!Note]
> Wenn Azure Data Explorer den Widerruf des Zugriffs auf einen kundenseitig verwalteten Schlüssel erkennt, wird der Cluster automatisch angehalten, um alle zwischengespeicherten Daten zu löschen. Nach Wiederherstellung des Zugriffs auf den Schlüssel muss der Cluster manuell fortgesetzt werden.

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Mithilfe der [rollenbasierten Zugriffssteuerung](/azure/role-based-access-control/overview) (Role-Based Access Control, RBAC) können Sie Aufgaben in Ihrem Team verteilen und den Zugriff von Clusterbenutzern auf das Nötigste beschränken. Sie haben die Möglichkeit, nur bestimmte Aktionen zuzulassen, anstatt allen Benutzern uneingeschränkten Zugriff auf den virtuellen Computer zu gewähren. Sie können die [Zugriffssteuerung für die Datenbanken](manage-database-permissions.md) über das [Azure-Portal](/azure/role-based-access-control/role-assignments-portal), mithilfe der [Azure-Befehlszeilenschnittstelle](/azure/role-based-access-control/role-assignments-cli) oder mit [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell) konfigurieren.

## <a name="next-steps"></a>Nächste Schritte

* [Sichern von Clustern in Azure Data Explorer mithilfe von Disk Encryption (Portal)](cluster-disk-encryption.md) durch Aktivieren der Verschlüsselung ruhender Daten
* [Konfigurieren verwalteter Identitäten für Ihren Azure Data Explorer-Cluster](managed-identities.md)
* [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe der Azure Resource Manager-Vorlage](customer-managed-keys-resource-manager.md)
* [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe von C#](customer-managed-keys-csharp.md)

