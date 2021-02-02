---
title: Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy für Azure Data Explorer
description: Listet Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy auf, die für Azure Data Explorer verfügbar sind. Diese integrierten Richtliniendefinitionen bieten allgemeine Ansätze für die Verwaltung der Compliance Ihrer Azure-Ressourcen.
ms.date: 01/27/2021
ms.topic: sample
author: orspod
ms.author: orspodek
ms.service: data-explorer
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 3d27a642afd13e25fb593a3ca79bf63e78aa408f
ms.sourcegitcommit: c65080267f32f23205134c4d685908db58fb87c0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99099168"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-data-explorer"></a>Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy für Azure Data Explorer

[Die Einhaltung gesetzlicher Bestimmungen in Azure Policy](/azure/governance/policy/concepts/regulatory-compliance) bietet von Microsoft erstellte und verwaltete Initiativendefinitionen (als _integriert_ bezeichnet) für die **Compliancedomänen** und **Sicherheitskontrollen**, die mit unterschiedlichen Compliancestandards zusammenhängen. Auf dieser Seite werden die **Compliancedomänen** und **Sicherheitskontrollen** für Azure Data Explorer aufgeführt. Sie können die integrierten Elemente für eine **Sicherheitskontrolle** einzeln zuweisen, um Ihre Azure-Ressourcen mit dem jeweiligen Standard kompatibel zu machen.

Die Titel der einzelnen integrierten Richtliniendefinitionen sind Links zur entsprechenden Richtliniendefinition im Azure-Portal. Verwenden Sie den Link in der Spalte **Richtlinienversion**, um die Quelle im [Azure Policy-GitHub-Repository](https://github.com/Azure/azure-policy) anzuzeigen.

> [!IMPORTANT]
> Jede Steuerung unten ist einer oder mehreren [Azure Policy](/azure/governance/policy/overview)-Definitionen zugeordnet. Diese Richtlinien können Ihnen bei der [Konformitätsbewertung](/azure/governance/policy/how-to/get-compliance-data) mit der Steuerung helfen. Es gibt jedoch oft keine 1:1- oder vollständige Übereinstimmung zwischen einer Steuerung und einer bzw. mehreren Richtlinien. Daher bezieht sich **Konform** in Azure Policy nur auf die Richtlinien selbst und gewährleistet nicht die vollständige Konformität mit allen Anforderungen einer Steuerung. Außerdem enthält der Kompatibilitätsstandard Steuerungen, die derzeit von keiner Azure Policy-Definition abgedeckt werden. Daher ist die Konformität in Azure Policy nur eine partielle Ansicht Ihres gesamten Konformitätsstatus. Die Zuordnungen zwischen Steuerungen und gesetzlichen Konformitätsdefinitionen von Azure Policy für diese Konformitätsstandards können sich im Laufe der Zeit ändern.

## <a name="cmmc-level-3"></a>CMMC Level 3

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Integrationen für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: CMMC Level 3](/azure/governance/policy/samples/cmmc-l3). Weitere Informationen zu diesem Konformitätsstandard finden Sie unter [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|System- und Kommunikationsschutz |SC.3.177 |Nutzen Sie FIPS-konforme Kryptografie zum Schützen der Vertraulichkeit von nicht klassifizierten kontrollierten Informationen (Controlled Unclassified Information, CUI). |[Azure Data Explorer-Verschlüsselung ruhender Daten muss einen kundenseitig verwalteten Schlüssel verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|System- und Kommunikationsschutz |SC.3.177 |Nutzen Sie FIPS-konforme Kryptografie zum Schützen der Vertraulichkeit von nicht klassifizierten kontrollierten Informationen (Controlled Unclassified Information, CUI). |[Datenträgerverschlüsselung muss in Azure Data Explorer aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|System- und Kommunikationsschutz |SC.3.177 |Nutzen Sie FIPS-konforme Kryptografie zum Schützen der Vertraulichkeit von nicht klassifizierten kontrollierten Informationen (Controlled Unclassified Information, CUI). |[Doppelte Verschlüsselung muss für Azure Data Explorer aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|System- und Kommunikationsschutz |SC.3.191 |Schützen Sie die Vertraulichkeit von nicht klassifizierten kontrollierten Informationen (Controlled Unclassified Information, CUI) im ruhenden Zustand. |[Datenträgerverschlüsselung muss in Azure Data Explorer aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|System- und Kommunikationsschutz |SC.3.191 |Schützen Sie die Vertraulichkeit von nicht klassifizierten kontrollierten Informationen (Controlled Unclassified Information, CUI) im ruhenden Zustand. |[Doppelte Verschlüsselung muss für Azure Data Explorer aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [Einhaltung gesetzlicher Bestimmungen in Azure Policy](/azure/governance/policy/concepts/regulatory-compliance)
- Sehen Sie sich die Integrationen im [Azure Policy-GitHub-Repository](https://github.com/Azure/azure-policy) an.
