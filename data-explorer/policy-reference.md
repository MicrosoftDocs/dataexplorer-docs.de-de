---
title: Integrierte Richtliniendefinitionen für Azure Data Explorer
description: Auflistung der integrierten Azure Policy-Richtliniendefinitionen für Azure Data Explorer Diese integrierten Richtliniendefinitionen bieten allgemeine Ansätze für die Verwaltung von Azure-Ressourcen.
ms.date: 11/17/2020
ms.topic: reference
author: orspod
ms.author: orspodek
ms.service: data-explorer
ms.custom: subject-policy-reference
ms.openlocfilehash: 9f2e98a1be83be659c8f7b85283c868d96f999eb
ms.sourcegitcommit: 0820454feb02ae489f3a86b688690422ae29d788
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94937907"
---
# <a name="azure-policy-built-in-definitions-for-azure-data-explorer"></a>Integrierte Azure Policy-Richtliniendefinitionen für Azure Data Explorer

Diese Seite enthält einen Index der integrierten [Azure Policy](/azure/governance/policy/overview)-Richtliniendefinitionen für Azure Data Explorer Weitere Azure Policy-Integrationen für andere Dienste finden Sie unter [Integrierte Azure Policy-Richtliniendefinitionen](/azure/governance/policy/samples/built-in-policies).

Die Namen der einzelnen integrierten Richtliniendefinitionen sind Links zur entsprechenden Richtliniendefinition im Azure-Portal. Verwenden Sie den Link in der Spalte **Version**, um die Quelle im [Azure Policy-GitHub-Repository](https://github.com/Azure/azure-policy) anzuzeigen.

## <a name="azure-data-explorer"></a>Azure-Daten-Explorer

|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Explorer-Verschlüsselung ruhender Daten muss einen kundenseitig verwalteten Schlüssel verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Das Aktivieren der Verschlüsselung ruhender Daten mit einem kundenseitig verwalteten Schlüssel in Ihrem Azure Data Explorer-Cluster bietet zusätzliche Kontrolle über den Schlüssel, der von der Verschlüsselung ruhender Schlüssel verwendet wird. Diese Funktion gilt häufig für Kunden mit speziellen Complianceanforderungen und erfordert eine Key Vault-Instanz zur Verwaltung der Schlüssel. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Datenträgerverschlüsselung muss in Azure Data Explorer aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |Durch das Aktivieren der Datenträgerverschlüsselung können Sie Ihre Daten schützen, um die Sicherheits- und Konformitätsverpflichtungen Ihrer Organisation zu erfüllen. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Doppelte Verschlüsselung muss für Azure Data Explorer aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |Durch das Aktivieren der doppelten Verschlüsselung können Sie Ihre Daten schützen, um die Sicherheits- und Konformitätsverpflichtungen Ihrer Organisation zu erfüllen. Wenn die doppelte Verschlüsselung aktiviert ist, werden Daten im Speicherkonto zweimal – einmal auf Dienstebene und einmal auf Infrastrukturebene – mit zwei unterschiedlichen Verschlüsselungsalgorithmen und zwei verschiedenen Schlüsseln verschlüsselt. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[VNET-Einschleusung muss für Azure Data Explorer aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Schützen Sie Ihren Netzwerkperimeter durch VNET-Einschleusung. So können Sie Regeln von Netzwerksicherheitsgruppen durchsetzen, lokale Verbindungen herstellen und Ihre Datenverbindungsquellen mit Dienstendpunkten absichern. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Integrationen im [Azure Policy-GitHub-Repository](https://github.com/Azure/azure-policy) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](/azure/governance/policy/concepts/definition-structure).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](/azure/governance/policy/concepts/effects).
