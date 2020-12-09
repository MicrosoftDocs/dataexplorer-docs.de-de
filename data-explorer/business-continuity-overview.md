---
title: Azure Data Explorer und Business Continuity & Disaster Recovery
description: In diesem Artikel werden Azure Data Explorer-Funktionen für die Wiederherstellung nach Störereignissen beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: ankhanol
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 2dcb1fb83d592ff7fd81fcfc5f51cf6fc95254b7
ms.sourcegitcommit: a36981785765b85a961f275be24d297d38e498fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96310033"
---
# <a name="business-continuity-and-disaster-recovery-overview"></a>Übersicht über Business Continuity & Disaster Recovery

Business Continuity & Disaster Recovery in Azure Data Explorer ermöglicht es Ihrem Unternehmen, den Geschäftsbetrieb im Falle einer Störung aufrechtzuerhalten. Dieser Artikel beschäftigt sich mit der Verfügbarkeit (innerhalb der Region) und der Notfallwiederherstellung. Er enthält Informationen zu nativen Funktionen und sowie zu Architekturaspekten für eine robuste Azure Data Explorer-Bereitstellung. Des Weiteren finden Sie hier Informationen zur Wiederherstellung nach menschlichen Fehlern, zu Hochverfügbarkeit und zu verschiedenen Notfallwiederherstellungskonfigurationen. Diese Konfigurationen sind abhängig von Resilienzanforderungen wie Recovery Point Objective (RPO) und Recovery Time Objective (RTO) sowie vom Aufwand und den Kosten.

## <a name="mitigate-disruptive-events"></a>Behandeln von Störereignissen

* [Menschlicher Fehler](#human-error)
* [Hochverfügbarkeit von Azure Data Explorer](#high-availability-of-azure-data-explorer)
* [Ausfall einer Azure-Verfügbarkeitszone](#outage-of-an-azure-availability-zone)
* [Ausfall eines Azure-Rechenzentrums](#outage-of-an-azure-datacenter)
* [Ausfall einer Azure-Region](#outage-of-an-azure-region)

### <a name="human-error"></a>Menschlicher Fehler 

Menschliche Fehler sind unvermeidlich. Benutzer können versehentlich einen Cluster, eine Datenbank oder eine Tabelle löschen.  

#### <a name="accidental-cluster-or-database-deletion"></a>Versehentliches Löschen eines Clusters oder einer Datenbank

Versehentlich gelöschte Cluster oder Datenbanken können nicht wiederhergestellt werden. Als Azure Data Explorer-Ressourcenbesitzer können Sie Datenverluste verhindern, indem Sie die auf der Azure-Ressourcenebene zur Verfügung stehende [Sperrfunktion](/azure/azure-resource-manager/management/lock-resources) für Löschvorgänge aktivieren.

#### <a name="accidental-table-deletion"></a>Versehentliches Löschen einer Tabelle

Benutzer, die mindestens über Tabellenadministratorberechtigungen verfügen, können [Tabellen löschen](kusto/management/drop-table-command.md). Sollte einer dieser Benutzer versehentlich eine Tabelle löschen, können Sie sie mithilfe des Befehls [`.undo drop table`](kusto/management/undo-drop-table-command.md) wiederherstellen. Dieser Befehl funktioniert allerdings nur, wenn zuvor die Wiederherstellbarkeitseigenschaft (*Recoverability*) in der [Aufbewahrungsrichtlinie](kusto/management/retentionpolicy.md) aktiviert wurde.

#### <a name="accidental-external-table-deletion"></a>Versehentliches Löschen einer externen Tabelle

[Externe Tabellen](kusto/query/schema-entities/externaltables.md) sind Entitäten des Kusto-Abfrageschemas, die auf Daten verweisen, die außerhalb der Datenbank gespeichert sind. Beim Löschen einer externen Tabelle werden lediglich die Tabellenmetadaten gelöscht. Sie können durch erneutes Ausführen des Tabellenerstellungsbefehls wiederhergestellt werden. Verwenden Sie die Funktion [Vorläufiges Löschen](/azure/storage/blobs/storage-blob-soft-delete), um sich vor dem versehentlichen Löschen oder Überschreiben einer Datei/eines Blobs zu schützen. Der Zeitraum kann dabei vom Benutzer konfiguriert werden.

### <a name="high-availability-of-azure-data-explorer"></a>Hochverfügbarkeit von Azure Data Explorer

Hochverfügbarkeit bezieht sich auf die Fehlertoleranz von Azure Data Explorer, der zugehörigen Komponenten und der zugrunde liegenden Abhängigkeiten innerhalb einer Azure-Region. Diese Fehlertoleranz vermeidet Single Points of Failure (SPOFs) in der Implementierung. In Azure Data Explorer umfasst Hochverfügbarkeit die Persistenzebene, die Computeebene und eine Leader-Follower-Konfiguration.

#### <a name="persistence-layer"></a>Persistenzebene

Azure Data Explorer nutzt Azure Storage als permanente Persistenzebene. Azure Storage sorgt automatisch für Fehlertoleranz und bietet in der Standardeinstellung lokal redundanten Speicher (LRS) in einem Rechenzentrum. Drei Replikate werden persistent gespeichert. Sollte ein Replikat während der Verwendung verloren gehen, wird ohne Unterbrechung ein weiteres bereitgestellt. Noch mehr Resilienz lässt sich mit zonenredundantem Speicher erreichen. Hierbei werden Replikate intelligent auf verschiedene regionale Azure-Verfügbarkeitszonen verteilt, um die Fehlertoleranz zu maximieren. Dies ist allerdings mit zusätzlichen Kosten verbunden.

#### <a name="compute-layer"></a>Computeebene

Bei Azure Data Explorer handelt es sich um eine Plattform für verteiltes Computing, die je nach Skalierung und Knotenrollentyp über zwei oder mehr Knoten verfügen kann. Wählen Sie zur Bereitstellungszeit Verfügbarkeitszonen aus, um die Knotenbereitstellung über mehrere Zonen zu verteilen und so eine möglichst hohe Resilienz innerhalb der Region zu erreichen. Der Ausfall einer Verfügbarkeitszone hat keinen vollständigen Ausfall zur Folge. Stattdessen verschlechtert sich lediglich die Leistung, bis die Zone wiederhergestellt wurde. 

#### <a name="leader-follower-cluster-configuration"></a>Leader-Follower-Clusterkonfiguration

Azure Data Explorer bietet eine optionale [Follower-Funktion](follower.md). Dadurch können Follower-Cluster einem Leader-Cluster folgen, um schreibgeschützten Zugriff auf die Daten und Metadaten des Leaders zu erhalten. Leader-Änderungen wie `create`, `append` und `drop` werden automatisch mit dem Follower synchronisiert. Leader können sich über mehrere Azure-Regionen erstrecken. Die Follower-Cluster müssen dagegen in der gleichen Region bzw. in den gleichen Regionen gehostet werden wie der Leader. Wenn der Leader-Cluster ausfällt oder Datenbanken/Tabellen versehentlich gelöscht werden, haben Follower-Cluster keinen Zugriff mehr, bis der Zugriff im Leader wiederhergestellt wurde. 

### <a name="outage-of-an-azure-availability-zone"></a>Ausfall einer Azure-Verfügbarkeitszone

Azure-Verfügbarkeitszonen sind individuelle physische Standorte innerhalb der gleichen Azure-Region. Sie dienen dazu, die Computeressourcen und Daten eines Azure Data Explorer-Clusters vor einem partiellen Regionsausfall zu schützen. Ein Zonenausfall ist ein Verfügbarkeitsszenario, da er sich innerhalb einer Region ereignet. 

Heften Sie einen Azure Data Explorer-Cluster an die gleiche Zone an wie andere verbundene Azure-Ressourcen. Weitere Informationen zur Aktivierung von Verfügbarkeitszonen finden Sie unter [Erstellen eines Clusters](create-cluster-database-portal.md#create-a-cluster).

> [!NOTE] 
> Verfügbarkeitszonen können nur zum Zeitpunkt der Clustererstellung ausgewählt werden, und die Auswahl kann später nicht mehr geändert werden.

### <a name="outage-of-an-azure-datacenter"></a>Ausfall eines Azure-Rechenzentrums

Da Azure-Verfügbarkeitszonen mit Kosten verbunden sind, entscheiden sich manche Kunden für eine Bereitstellung ohne Zonenredundanz. Bei einer solchen Azure Data Explorer-Bereitstellung zieht der Ausfall eines Azure-Rechenzentrums einen Clusterausfall nach sich. Der Ausfall eines Azure-Rechenzentrums muss daher auf die gleiche Weise behandelt werden wie der Ausfall einer Azure-Region.

### <a name="outage-of-an-azure-region"></a>Ausfall einer Azure-Region

Azure Data Explorer bietet keinen automatischen Schutz vor dem Ausfall einer gesamten Azure-Region. Um die geschäftlichen Auswirkungen bei einem solchen Ausfall zu minimieren, sollten mehrere Azure Data Explorer-Cluster in [Azure-Regionspaaren](/azure/best-practices-availability-paired-regions) verwendet werden. Es gibt [verschiedene Notfallwiederherstellungskonfiguration](#disaster-recovery-configurations), die jeweils von Recovery Time Objective (RTO), Recovery Point Objective (RPO), Aufwand und Kosten abhängig sind. Kosten- und Leistungsoptimierungen sind mit Azure Advisor-Empfehlungen und durch eine Konfiguration mit [Autoskalierung](manage-cluster-horizontal-scaling.md) möglich.

## <a name="disaster-recovery-configurations"></a>Notfallwiederherstellungskonfigurationen

 In diesem Abschnitt werden mehrere Notfallwiederherstellungskonfigurationen beschrieben, die jeweils von den Resilienzanforderungen (RPO und RTO) sowie vom Aufwand und von den Kosten abhängen.

Recovery Time Objective (RTO) bezieht sich auf die Zeit bis zur Wiederherstellung nach einer Störung. So bedeutet beispielsweise ein RTO-Wert von zwei Stunden, dass die Anwendung nach einer Störung innerhalb von zwei Stunden wieder verfügbar sein muss. Recovery Point Objective (RPO) bezieht sich auf die Zeit, die bei einer Störung vergehen kann, bis der Datenverlust den zulässigen Schwellenwert übersteigt. Wenn der RPO-Wert also beispielsweise 24 Stunden beträgt und eine Anwendung Daten aus den letzten 15 Jahren enthält, sind die Parameter des vereinbarten RPO-Werts weiterhin erfüllt.

Erfassungs-, Verarbeitungs- und Zusammenstellungsprozesse müssen bereits im Vorfeld der Notfallwiederherstellungsplanung sorgfältig entworfen werden. Erfassung bezieht sich auf Daten, die aus verschiedenen Quellen in Azure Data Explorer integriert werden. Verarbeitung bezieht sich auf Transformationen und ähnliche Aktivitäten. Zusammenstellung bezieht sich auf materialisierte Sichten, Exporte in den Data Lake und Ähnliches.

Hier finden Sie gängige Notfallwiederherstellungskonfigurationen, die im Anschluss jeweils ausführlich beschrieben werden:
* [Aktiv/Aktiv/Aktiv-Konfiguration (Always On)](#active-active-active-configuration)
* [Aktiv/Aktiv-Konfiguration](#active-active-configuration)
* [Konfiguration mit einem aktiven und einem unmittelbar betriebsbereiten Cluster](#active-hot-standby-configuration)
* [Konfiguration mit bedarfsgesteuertem Datenwiederherstellungscluster](#on-demand-data-recovery-configuration)

### <a name="active-active-active-configuration"></a>Aktiv/Aktiv/Aktiv-Konfiguration

Diese Konfiguration wird auch als „Always On“ bezeichnet. Bei kritischen Anwendungsbereitstellungen ohne Toleranz für Ausfälle empfiehlt sich die Verwendung mehrerer Azure Data Explorer-Cluster in verschiedenen Azure-Regionspaaren. Richten Sie die Erfassung, Verarbeitung und Zusammenstellung parallel zu allen Clustern ein. Die Cluster-SKU muss regionsübergreifend gleich sein. Von Azure wird sichergestellt, dass Updates in Azure-Regionspaaren angewendet und gestaffelt werden. Der Ausfall einer Azure-Region führt nicht zu einem Anwendungsausfall. Es kommt jedoch möglicherweise zu Wartezeiten oder Leistungseinbußen.

:::image type="content" source="media/business-continuity-overview/active-active-active-n.png" alt-text="Aktiv/Aktiv/Aktiv/n-Konfiguration":::

| **Konfiguration** | **RPO** | **RTO** | **Aufwand** | **Kosten** |
| --- | --- | --- | --- | --- |
| **Aktiv/Aktiv/Aktiv/-n** | 0 Stunden | 0 Stunden | Geringer | Maximal |

### <a name="active-active-configuration"></a>Aktiv/Aktiv-Konfiguration

Diese Konfiguration ist mit der [Aktiv/Aktiv/Aktiv-Konfiguration](#active-active-active-configuration) identisch, umfasst jedoch nur zwei Azure-Regionspaare. Konfigurieren Sie eine duale Erfassung, Verarbeitung und Zusammenstellung. Benutzer werden an die nächstgelegene Region weitergeleitet. Die Cluster-SKU muss regionsübergreifend gleich sein.

:::image type="content" source="media/business-continuity-overview/active-active.png" alt-text="Aktiv/Aktiv-Konfiguration":::

| **Konfiguration** | **RPO** | **RTO** | **Aufwand** | **Kosten** |
| --- | --- | --- | --- | --- |
| **Aktiv/Aktiv** | Keine | Keine | Geringer | Hoch |

### <a name="active-hot-standby-configuration"></a>Konfiguration mit einem aktiven und einem unmittelbar betriebsbereiten Cluster

Die Konfiguration mit einem aktiven und einem unmittelbar betriebsbereiten Cluster ähnelt der [Aktiv/Aktiv-Konfiguration](#active-active-configuration) hinsichtlich der dualen Erfassung, Verarbeitung und Zusammenstellung. Allerdings ist der Standbycluster für Endbenutzer offline und muss nicht der gleichen SKU angehören wie der primäre Cluster. Der unmittelbar betriebsbereite Cluster kann auch eine kleinere SKU und Skalierung aufweisen und somit weniger leistungsfähig sein. In einem Notfallszenario wird der Standbycluster online geschaltet und hochskaliert.

:::image type="content" source="media/business-continuity-overview/active-hot-standby.png" alt-text="Konfiguration mit einem aktiven und einem unmittelbar betriebsbereiten Cluster":::

| **Konfiguration** | **RPO** | **RTO** | **Aufwand** | **Kosten** |
| --- | --- | --- | --- | --- |
| **Ein aktiver und ein unmittelbar betriebsbereiter Cluster** | Niedrig | Niedrig | Medium | Medium |

### <a name="on-demand-data-recovery-configuration"></a>Konfiguration mit bedarfsgesteuerter Datenwiederherstellung

Diese Lösung bietet die geringste Resilienz (höchster RPO- und RTO-Wert) und die niedrigsten Kosten, ist aber mit dem höchsten Aufwand verbunden. In dieser Konfiguration gibt es keinen Datenwiederherstellungscluster. Konfigurieren Sie den fortlaufenden Export zusammengestellter Daten (es sei denn, es werden auch Roh- und Zwischendaten benötigt) für ein Speicherkonto mit GRS-Konfiguration (georedundanter Speicher). Ein Datenwiederherstellungscluster wird im Falle eines Notfallwiederherstellungsszenarios hochgefahren. Zu diesem Zeitpunkt werden DDLs, die Konfiguration, Richtlinien und Prozesse angewendet. Daten werden aus dem Speicher mit der Erfassungseigenschaft [kustoCreationTime](ingest-data-event-grid-overview.md) erfasst, um die Erfassungszeit zu überschreiben, die standardmäßig auf die Systemzeit festgelegt ist. 

:::image type="content" source="media/business-continuity-overview/on-demand-data-recovery-cluster.png" alt-text="Konfiguration mit bedarfsgesteuertem Datenwiederherstellungscluster":::

| **Konfiguration** | **RPO** | **RTO** | **Aufwand** | **Kosten** |
| --- | --- | --- | --- | --- |
| **Bedarfsgesteuerter Datenwiederherstellungscluster** | Maximal | Maximal | Maximal | Niedrigste |

### <a name="summary-of-disaster-recovery-configuration-options"></a>Zusammenfassung der Optionen für die Notfallwiederherstellungskonfiguration

| **Konfiguration** | **Resilienz** | **RPO** | **RTO** | **Aufwand** | **Kosten** |
| --- | --- | --- | --- | --- | --- |
| **Aktiv/Aktiv/Aktiv/-n** | Maximal | 0 Stunden | 0 Stunden | Geringer | Maximal |
| **Aktiv/Aktiv** | Hoch | Keine | Keine | Geringer | Hoch |
| **Ein aktiver und ein unmittelbar betriebsbereiter Cluster** | Medium | Niedrig | Niedrig | Medium | Medium |
| **Bedarfsgesteuerter Datenwiederherstellungscluster** | Niedrigste | Maximal | Maximal | Maximal | Niedrigste |

## <a name="best-practices"></a>Bewährte Methoden

Die folgenden bewährten Methoden sollten unabhängig von der gewählten Notfallwiederherstellungskonfiguration angewendet werden:

* Alle Datenbankobjekte, -richtlinien und -konfigurationen sollten in der Quellcodeverwaltung gespeichert werden, um sie per Releaseautomatisierungstool für den Cluster veröffentlichen zu können. Weitere Informationen finden Sie unter [Azure DevOps-Aufgabe für Azure Data Explorer](devops.md). 
* Entwerfen, entwickeln und implementieren Sie Validierungsroutinen, um sicherzustellen, dass die Daten aller Cluster synchronisiert sind. Von Azure Data Explorer werden [clusterübergreifende Verknüpfungen](kusto/query/cross-cluster-or-database-queries.md?pivots=azuredataexplorer) unterstützt. Eine einfache tabellenübergreifende Zeilenzählung kann zur Validierung beitragen.
* Verwenden Sie die Funktion [Fortlaufender Export](kusto/management/data-export/continuous-data-export.md), und exportieren Sie Daten aus Azure Data Explorer-Tabellen in einen Azure Data Lake-Speicher. Verwenden Sie GRS, um die höchstmögliche Resilienz zu erreichen.
* Die Releaseprozeduren sollten Governanceüberprüfungen enthalten, die die Spiegelung der Cluster sicherstellen.
* Machen Sie sich umfassend mit den Anforderungen für die Neuerstellung eines Clusters vertraut.
* Erstellen Sie eine Prüfliste mit Bereitstellungseinheiten. Diese Liste variiert zwar abhängig von Ihren individuellen Anforderungen, sollte aber Folgendes enthalten: Bereitstellungsskripts, Erfassungsverbindungen, BI-Tools und andere wichtige Konfigurationen. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Erstellen von BCDR-Lösungen (Business Continuity & Disaster Recovery) mit Azure Data Explorer](business-continuity-create-solution.md).
