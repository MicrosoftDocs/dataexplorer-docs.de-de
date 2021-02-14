---
title: Azure-Sicherheitsbaseline für Azure Data Explorer
description: Die Azure Data Explorer-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen im Azure-Sicherheitsvergleichstest.
author: msmbaldwin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c6cf97837f0da34a63f3b9274d160650ab2349fb
ms.sourcegitcommit: c11e3871d600ecaa2824ad78bce9c8fc5226eef9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554925"
---
# <a name="azure-security-baseline-for-azure-data-explorer"></a>Azure-Sicherheitsbaseline für Azure Data Explorer

Diese Sicherheitsbaseline wendet Empfehlungen des [Azure-Sicherheitsvergleichstests Version 1.0](/azure/security/benchmarks/overview-v1) auf Azure Data Explorer an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure.
Der Inhalt wird nach den **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und die entsprechenden für Azure Data Explorer geltenden Empfehlungen definiert werden. Nicht auf Azure Data Explorer anwendbare **Kontrollen** wurden ausgeschlossen.

 
Die vollständige Zuordnung von Azure Data Explorer zum Azure-Sicherheitsvergleichstest finden Sie in der [vollständigen Zuordnungsdatei der Azure Data Explorer-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Azure Data Explorer unterstützt die Bereitstellung eines Clusters in einem Subnetz in Ihrem virtuellen Netzwerk. Diese Funktionalität ermöglicht es Ihnen, NSG-Regeln (Netzwerksicherheitsgruppe) für den Datenverkehr Ihres Azure Data Explorer-Clusters durchzusetzen, Ihr lokales Netzwerk mit dem Subnetz des Azure Data Explorer-Clusters zu verbinden und Ihre Datenverbindungsquellen (Event Hub und Event Grid) mit Dienstendpunkten zu sichern.

- [Erstellen eines Clusters im virtuellen Netzwerk](./vnet-create-cluster-portal.md)

- [Bereitstellen Ihres Azure Data Explorer-Clusters in einem virtuellen Netzwerk](./vnet-deployment.md)

- [Behandeln von Problemen mit der VNET-Clustererstellung, -konnektivität und -verwendung](./vnet-deploy-troubleshoot.md?tabs=windows)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkschnittstellen

**Leitfaden**: Aktivieren Sie Netzwerksicherheitsgruppen-Flussprotokolle (NSG), und senden Sie Protokolle an ein Speicherkonto, um den Datenverkehr zu überwachen.

- [Aktivieren der NSG-Flussprotokolle](/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Grundlegendes zur Netzwerksicherheit über das Azure Security Center](/azure/security-center/security-center-network-recommendations)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten schädlichen IP-Adressen

**Leitfaden**: Aktivieren Sie Azure DDoS Protection Standard im virtuellen Netzwerk, um Ihre Azure Data Explorer-Cluster vor DDoS-Angriffen zu schützen. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

- [Konfigurieren von DDoS-Schutz](/azure/virtual-network/manage-ddos-protection)

- [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Aktivieren Sie die Flussprotokolle für die Netzwerksicherheitsgruppe (NSG), die zum Schutz Ihres Azure Data Explorer-Clusters verwendet wird, und senden Sie Protokolle an ein Speicherkonto für die Überwachung des Datenverkehrs.

- [Aktivieren der NSG-Flussprotokolle](/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Virtual Network-Diensttags, um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen oder Azure Firewalls zu definieren, die Ihren Azure Data Explorer-Clustern zugeordnet sind. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

- [Diensttags in virtuellen Netzwerken](/azure/virtual-network/service-tags-overview)

- [Konfigurationsanforderungen für Diensttags für Azure Data Explorer](./vnet-deployment.md#dependencies-for-vnet-deployment)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Der Kunde definiert und implementiert Standardsicherheitskonfigurationen für Netzwerkressourcen mit Azure Policy.

Kunden können auch mit Azure Blueprints umfangreiche Azure-Bereitstellungen vereinfachen, indem wichtige Umgebungsartefakte wie Azure Resource Manager-Vorlagen, Azure RBAC-Steuerungen und Azure Policy-Zuweisungen gemeinsam in einer Blaupausendefinition gepackt werden. Sie können die Blaupause leicht auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

- [Konfigurieren und Verwalten von Azure Policy](/azure/governance/policy/tutorials/create-and-manage)

- [Erstellen einer Azure-Blaupause](/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerksicherheitsgruppen (NSGs) und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss für Ihre Azure Data Explorer-Cluster. Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

- [Erstellen und Verwenden von Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie Azure Policy, um die Konfiguration für Netzwerkressourcen zu überprüfen (und/oder zu korrigieren).

- [Konfigurieren und Verwalten von Azure Policy](/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: In Azure Data Explorer werden Diagnoseprotokolle verwendet, um Erkenntnisse zu erfolgreichen und fehlgeschlagenen Erfassungsvorgängen zu gewinnen. Sie können Vorgangsprotokolle in Azure Storage, Event Hub oder Log Analytics exportieren, um den Erfassungsstatus zu überwachen.

- [Überwachen der Azure Data Explorer-Erfassungsvorgänge](./using-diagnostic-logs.md)

- [Erfassen und Abfragen von Überwachungsdaten in Azure Data Explorer](./ingest-data-no-code.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie Diagnoseeinstellungen für Azure Data Explorer für den Zugriff auf und die Protokollierung von dienstspezifischen Vorgängen und die Protokollierung. Azure-Aktivitätsprotokolle innerhalb von Azure Monitor, die auch allgemeine Protokolle über die Ressource enthalten, sind standardmäßig aktiviert.

- [Überwachen der Azure Data Explorer-Erfassungsvorgänge](./using-diagnostic-logs.md)

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Übersicht über Protokolle der Azure-Plattform](/azure/azure-monitor/platform/platform-logs-overview)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige/Archivierungsspeicherung.

- [Ändern des Datenaufbewahrungszeitraums](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle auf anomales Verhalten, und prüfen Sie die Ergebnisse regelmäßig. Nachdem Sie die Diagnoseeinstellungen für Azure Data Explorer aktiviert haben, verwenden Sie den Log Analytics-Arbeitsbereich von Azure Monitor, um Protokolle zu überprüfen und Abfragen für Protokolldaten durchzuführen.

- [Grundlegendes zum Log Analytics-Arbeitsbereich](/azure/azure-monitor/log-query/get-started-portal)

- [Ausführen benutzerdefinierter Abfragen in Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: In Azure Data Explorer definieren Sicherheitsrollen, welche Sicherheitsprinzipale (Benutzer und Anwendungen) die Berechtigung erhalten, mit einer gesicherten Ressource wie einer Datenbank oder einer Tabelle zu arbeiten, und welche Vorgänge zulässig sind. Sie können die Kusto-Abfrage verwenden, um Prinzipien der Administratorrolle für die Azure Data Explorer-Cluster und -Datenbanken aufzulisten.

- [Sicherheitsrollenverwaltung in Azure Data Explorer mithilfe von Kusto-Abfragen](/azure/kusto/management/security-roles)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Azure Active Directory (Azure AD) verfolgt nicht das Konzept der Standardkennwörter. Bei anderen Azure-Ressourcen, für die ein Kennwort erforderlich ist, wird erzwungen, dass ein Kennwort mit komplexen Anforderungen und einer minimalen Kennwortlänge, die sich je nach Dienst unterscheiden, erstellt wird. Sie sind verantwortlich für Anwendungen und Marketplace-Dienste von Drittanbietern, bei denen möglicherweise Standardkennwörter verwendet werden.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Kunden können Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten erstellen. Verwenden Sie die Identitäts- und Zugriffsverwaltung in Azure Security Center, um die Anzahl der Administratorkonten zu überwachen.

Kunden können außerdem einen Just-In-Time- oder Just-Enough-Zugriff aktivieren, indem sie mit Azure Active Directory (Azure AD) Privileged Identity Management verwaltete privilegierte Rollen für Microsoft-Dienste und ARM verwenden. 

- [Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Verwenden des einmaligen Anmeldens (SSO) in Azure Active Directory

**Leitfaden**: Wo immer möglich, sollte der Kunde das einmalige Anmelden (SSO) mit Azure Active Directory (Azure AD) verwenden, anstatt einzelne eigenständige Anmeldeinformationen pro Dienst zu konfigurieren. Wenden Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center an.

- [Grundlegendes zu SSO mit Azure AD](/azure/active-directory/manage-apps/what-is-single-sign-on)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für Azure Active Directory (Azure AD), und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

- [Aktivieren von MFA in Azure](/azure/active-directory/authentication/howto-mfa-getstarted)

- [Überwachen von Identität und Zugriff in Azure Security Center](/azure/security-center/security-center-identity-access)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Verwenden von sicheren, von Azure verwalteten Arbeitsstationen für Verwaltungsaufgaben

**Leitfaden**: Verwenden Sie Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAW) mit Multi-Factor Authentication (MFA), die für die Anmeldung und Konfiguration von Azure-Ressourcen konfiguriert sind.

- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Aktivieren von MFA in Azure](/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center-Überwachung:** Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte (Azure AD) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der Umgebung. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](/azure/security-center/security-center-identity-access)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Kunden können benannte Standorte mit bedingtem Zugriff verwenden, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

- [Konfigurieren benannter Standorte in Azure](/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Azure Active Directory (Azure AD) ist die bevorzugte Methode zur Authentifizierung für Azure Data Explorer. Azure AD unterstützt eine Reihe von Authentifizierungsszenarien:

- Benutzerauthentifizierung (interaktive Anmeldung): Dient zur Authentifizierung menschlicher Prinzipale.

- Anwendungsauthentifizierung (nicht interaktive Anmeldung): Dient zur Authentifizierung von Diensten und Anwendungen, die ohne Interaktion mit einem menschlichen Benutzer ausgeführt/authentifiziert werden müssen.

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Übersicht über Azure Data Explorer Access Control](/azure/kusto/management/access-control)

- [Authentifizieren mit Azure Active Directory](/azure/kusto/management/access-control/aad)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (Azure AD) enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff besitzen. 

- [Authentifizieren mit Azure AD für den Azure Data Explorer-Zugriff](/azure/kusto/management/access-control/how-to-authenticate-with-aad)

- [Azure AD-Berichterstellung](/azure/active-directory/reports-monitoring/)

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Sie können die Protokollquellen von Azure Active Directory-Anmeldeaktivitäten (Azure AD) und von Überwachungs- und Risikoereignissen zur Überwachung verwenden, sodass die Integration in jedes SIEM- (Security Information and Event Management) und Überwachungstool möglich ist.

 Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten erstellen sowie die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Kunden können gewünschte Warnungen im Log Analytics-Arbeitsbereich konfigurieren.

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit den Funktionen zur Risikoerkennung und zum Identitätsschutz von Azure Active Directory (Azure AD) können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

- [Anzeigen riskanter Azure AD-Anmeldungen](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: In Supportszenarien, bei denen Microsoft auf Kundendaten zugreifen muss, wird per Kunden-Lockbox eine Benutzeroberfläche bereitgestellt, auf der Kunden Anforderungen des Zugriffs auf Kundendaten prüfen und dann genehmigen oder ablehnen können.

- [Grundlegendes zu Kunden-Lockbox](/azure/security/fundamentals/customer-lockbox-overview)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz von Daten](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

- [Erstellen und Verwenden von Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Azure Data Explorer-Cluster sollten von anderen Ressourcen durch ein virtuelles Netzwerk/Subnetz getrennt, entsprechend gekennzeichnet und innerhalb einer Netzwerksicherheitsgruppe (NSG) oder Azure Firewall gesichert werden. Azure Data Explorer-Cluster, die vertrauliche Daten speichern oder verarbeiten, müssen ausreichend isoliert werden.

- [Erstellen zusätzlicher Azure-Abonnements](/azure/billing/billing-create-subscription)

- [Erstellen von Verwaltungsgruppen](/azure/governance/management-groups/create)

- [Erstellen und Verwenden von Tags](/azure/azure-resource-manager/resource-group-using-tags)

- [Bereitstellen Ihres Azure Data Explorer-Clusters in einem virtuellen Netzwerk](./vnet-deployment.md)

- [Erstellen einer NSG mit einer Sicherheitskonfiguration](/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Azure Data Explorer-Cluster handeln standardmäßig TLS 1.2 aus. Stellen Sie sicher, dass alle Clients, die sich mit Ihren Azure-Ressourcen verbinden, TLS 1.2 oder höher aushandeln können.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen zur Datenidentifizierung und -klassifizierung sowie zur Vermeidung von Datenverlust sind für Azure Data Explorer noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](/azure/security/fundamentals/protection-customer-data)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Verwenden der rollenbasierten Zugriffssteuerung zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Azure Data Explorer ermöglicht Ihnen über das Modell für die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) die Steuerung des Zugriffs auf Datenbanken und Tabellen. Bei diesem Modell werden Prinzipale (Benutzer, Gruppen und Apps) Rollen zugewiesen. Prinzipale können gemäß den zugewiesenen Rollen auf Ressourcen zugreifen.

- [Liste der Rollen, Berechtigungen und Anweisungen zur Konfiguration von RBAC für Azure Data Explorer](./manage-database-permissions.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Azure Disk Encryption unterstützt Sie beim Schutz Ihrer Daten gemäß den Sicherheits- und Complianceanforderungen Ihrer Organisation. Der Dienst bietet Volumeverschlüsselung für das Betriebssystem und die Datenträger Ihrer virtuellen Clustercomputer. Azure Disk Encryption ist außerdem mit Azure Key Vault integriert, um die Schlüssel und Geheimnisse für die Datenträgerverschlüsselung steuern und verwalten zu können. So wird sichergestellt, dass alle ruhenden Daten auf den Datenträgern von virtuellen Computern in Azure Storage verschlüsselt sind.

- [Aktivieren der Verschlüsselung ruhender Daten für Azure Data Explorer-Cluster](./cluster-disk-encryption.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen zu erstellen, wenn Änderungen auf Ressourcenebene auf Ihren Azure Data Explorer-Clustern erfolgen.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Verwenden Sie die Standardrisikobewertungen (Secure Score), die von Azure Security Center bereitgestellt werden.

- [Grundlegendes zum Secure Score des Azure Security Center](/azure/security-center/security-center-secure-score)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie entsprechende (Lese-) Berechtigungen in Ihrem Mandanten sicher, und zählen Sie alle Azure-Abonnements sowie Ressourcen in ihren Abonnements auf.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

- [Erstellen von Abfragen mit Azure Resource Graph](/azure/governance/resource-graph/first-query-portal)

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Grundlegendes zu Azure RBAC](/azure/role-based-access-control/overview)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

- [Erstellen und Verwenden von Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Sie können geeignete Namenskonventionen, Kennzeichnungen, Verwaltungsgruppen oder ggf. separate Abonnements verwenden, um Ressourcen zu organisieren und nachzuverfolgen. Sie können Azure Resource Graph verwenden, um den Bestand regelmäßig abzustimmen und sicherzustellen, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden. 

- [Erstellen zusätzlicher Azure-Abonnements](/azure/billing/billing-create-subscription)

- [Erstellen von Verwaltungsgruppen](/azure/governance/management-groups/create)

- [Erstellen und Verwenden von Tags](/azure/azure-resource-manager/resource-group-using-tags)

- [Erstellen von Abfragen mit Azure Resource Graph](/azure/governance/resource-graph/first-query-portal)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Sie müssen einen Bestand an genehmigten Azure-Ressourcen und genehmigter Software für Computeressourcen gemäß Ihren organisatorischen Anforderungen erstellen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Sie können Azure-Richtlinien verwenden, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Sie können die von der Richtlinie generierten Ereignisse mithilfe der Aktivitätsprotokolle überwachen, die mithilfe von Azure Monitor überwacht werden können.

Darüber hinaus können Sie Azure Resource Graph verwenden, um Ressourcen in den Abonnements abzufragen und zu ermitteln.

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](/azure/governance/policy/tutorials/create-and-manage)

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](/azure/governance/resource-graph/first-query-portal)

- [Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center-Überwachung:** Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Sie können Azure-Richtlinien verwenden, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

 - Not allowed resource types (Unzulässige Ressourcentypen)

 - Zulässige Ressourcentypen

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy-Beispiele](/azure/governance/policy/samples/built-in-policies#general)

**Azure Security Center-Überwachung:** Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur Verwaltung von Microsoft Azure konfigurieren. Dadurch wird das Erstellen und Ändern von Ressourcen innerhalb Ihrer Azure-Abonnements verhindert. 

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zur Überprüfung oder Durchsetzung der Konfiguration Ihrer Azure-Ressourcen zu erstellen. Sie können auch integrierte Azure Policy-Definitionen verwenden.

Außerdem hat Azure Resource Manager die Möglichkeit, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren, der zur Sicherstellung überprüft werden sollte, dass die Konfigurationen die Sicherheitsanforderungen für Ihre Organisation erfüllen oder übertreffen.

Sie können auch Empfehlungen von Azure Security Center als sichere Konfigurationsbaseline für Ihre Azure-Ressourcen verwenden.

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](/azure/governance/policy/tutorials/create-and-manage)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](/azure/azure-resource-manager/templates/export-template-portal)

- [Sicherheitsempfehlungen: Referenzhandbuch](/azure/security-center/recommendations-reference)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure-Richtlinien [Verweigern] und [Bereitstellen, falls nicht vorhanden], um sichere Einstellungen in den Azure-Ressourcen zu erzwingen.  Sie können Lösungen wie Änderungsnachverfolgung, das Dashboard für die Richtlinienkompatibilität oder eine benutzerdefinierte Lösung verwenden, um Sicherheitsänderungen in Ihrer Umgebung leicht zu erkennen.

- [Grundlegendes zu Azure Policy-Auswirkungen](/azure/governance/policy/concepts/effects)

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](/azure/governance/policy/tutorials/create-and-manage)

- [Nachverfolgen von Änderungen in Ihrer Umgebung mit der Änderungsnachverfolgung](/azure/automation/change-tracking)

- [Abrufen von Compliancedaten von Azure-Ressourcen](/azure/governance/policy/how-to/get-compliance-data)

**Azure Security Center-Überwachung:** Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Repos, um Ihren Code sicher zu speichern und wie benutzerdefinierte Azure-Richtlinien, Azure Resource Manager-Vorlagen, Desired State Configuration-Skripts usw. zu verwalten. Um auf die Ressourcen zuzugreifen, die Sie in Azure DevOps verwalten, können Sie bestimmten Benutzern, integrierten Sicherheitsgruppen oder in Azure Active Directory (Azure AD) definierte Gruppen (falls in Azure DevOps integriert) oder in Active Directory definierte Gruppen (falls in TFS integriert), Berechtigungen gewähren oder verweigern.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Informationen über Berechtigungen und Gruppen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Azure-Ressourcen mit Azure Policy. Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zur Überprüfung oder Durchsetzung der Netzwerkkonfiguration Ihrer Azure-Ressourcen zu erstellen. Sie können auch auf integrierte Richtliniendefinitionen zurückgreifen, die sich auf Ihre spezifischen Ressourcen beziehen.  Zusätzlich können Sie Azure Automation verwenden, um Konfigurationsänderungen bereitzustellen.

- [Konfigurieren und Verwalten von Azure Policy](/azure/governance/policy/tutorials/create-and-manage)

- [Aliase](/azure/governance/policy/concepts/definition-structure#aliases)

**Azure Security Center-Überwachung:** Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Verwenden Sie die Azure Policy-Einstellungen [audit] (überwachen), [deny] (verweigern) und [deploy if not existent] (bereitstellen, falls nicht vorhanden), um Konfigurationen für Ihre Azure-Ressourcen automatisch zu erzwingen.

- [Konfigurieren und Verwalten von Azure Policy](/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure Active Directory (AAD) bereitzustellen. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

- [Konfigurieren von verwalteten Identitäten](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

- [Konfigurieren von verwalteten Identitäten für Ihren Azure Data Explorer-Cluster](./managed-identities.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen. 

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz vor Schadsoftware](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Data Explorer) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Inhalte durch, die in Azure-Ressourcen ohne Computeaufgaben hochgeladen werden sollen, z. B. Azure Data Explorer, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL usw. Microsoft kann auf diesen Instanzen nicht auf Ihre Daten zugreifen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Die Daten in Ihrem Microsoft Azure-Speicherkonto, die von Ihrem Azure Data Explorer-Cluster verwendet werden, werden stets repliziert, um Beständigkeit und Hochverfügbarkeit sicherzustellen. Azure Storage kopiert Ihre Daten so, dass sie vor geplanten und ungeplanten Ereignissen geschützt sind – von vorübergehend auftretenden Hardwarefehlern und Netzwerk- oder Stromausfällen bis zu schweren Naturkatastrophen usw. Sie können Ihre Daten wahlweise im selben Rechenzentrum, Rechenzentren in derselben Region und sogar übergreifend über geografisch getrennte Regionen replizieren.

- [Grundlegendes zu Azure Storage-Redundanz und Vereinbarungen zum Servicelevel](/azure/storage/common/storage-redundancy)

- [Exportieren von Daten in den Speicher](/azure/kusto/management/data-export/export-data-to-storage)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Azure Data Explorer verschlüsselt alle Daten in einem Speicherkonto für ruhende Daten. Standardmäßig werden Daten mit von Microsoft verwalteten Schlüsseln verschlüsselt. Um zusätzliche Kontrolle über die Verschlüsselungsschlüssel zu erhalten, können Sie kundenseitig verwaltete Schlüssel für die Datenverschlüsselung bereitstellen. Vom Kunden verwaltete Schlüssel müssen in Azure Key Vault gespeichert werden.

- [Konfigurieren von kundenseitig verwalteten Schlüsseln über das Azure-Portal](./customer-managed-keys-portal.md)

- [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe von C#](./customer-managed-keys-csharp.md)

- [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe der Azure Resource Manager-Vorlage](./customer-managed-keys-resource-manager.md)

- [Sichern von Azure Key Vault-Zertifikaten](/powershell/module/az.keyvault/backup-azkeyvaultcertificate?preserve-view=true&view=azps-4.8.0)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Testen Sie regelmäßig die Datenwiederherstellung Ihrer Azure Key Vault-Geheimnisse.

- [Wiederherstellen von Azure Key Vault-Zertifikaten](/powershell/module/az.keyvault/restore-azkeyvaultcertificate?preserve-view=true&view=azps-4.8.0)

- [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe von C#](./customer-managed-keys-csharp.md)

- [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe der Azure Resource Manager-Vorlage](./customer-managed-keys-resource-manager.md)

**Azure Security Center-Überwachung:** Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Kunden können vorläufiges Löschen in Key Vault aktivieren, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.  Sie können auch den Bereinigungsschutz aktivieren, sodass ein Schlüsseltresor oder ein Objekt im gelöschten Zustand erst nach Ablauf des Aufbewahrungszeitraums endgültig gelöscht werden kann.  

- [Aktivieren des vorläufigen Löschens und des Löschschutzes in Key Vault](/azure/key-vault/key-vault-ovw-soft-delete)

- [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe von C#](./customer-managed-keys-csharp.md)

- [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe der Azure Resource Manager-Vorlage](./customer-managed-keys-resource-manager.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.
    

- [Anleitung zum Entwickeln Ihres eigenen Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

    

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

    

- [Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Incidents auszuarbeiten.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-Überwachung:** Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion) mithilfe von Tags, und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren, insbesondere solche, die vertrauliche Daten verarbeiten.  Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

- [Sicherheitswarnungen in Azure Security Center](/azure/security-center/security-center-alerts-overview)

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Übungen durch, um die Reaktionsfähigkeit Ihrer Systeme zu testen und so Ihre Azure-Ressourcen zu schützen. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.
    

- [ Informationen finden Sie in der folgenden Veröffentlichung des NIST: des National Institute of Standards and Technology (NIST)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung:** Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.
    

- [Festlegen der Kontaktinformationen in Azure Security Center](/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“, um Risiken für Azure-Ressourcen zu ermitteln. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.
    

- [Konfigurieren des fortlaufenden Exports](/azure/security-center/continuous-export)

    

- [Streamen von Warnungen in Azure Sentinel](/azure/sentinel/connect-azure-security-center)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über Logic Apps automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen und dadurch Ihre Azure-Ressourcen noch besser zu schützen.
    

- [ Konfigurieren von Workflowautomatisierung und Logic Apps](/azure/security-center/workflow-automation)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie in dem Fall die Einsatzregeln für Penetrationstests von Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen. 

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Übersicht über Version 2 des Azure-Sicherheitsvergleichstests](/azure/security/benchmarks/overview) an.
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](/azure/security/benchmarks/security-baselines-overview).
