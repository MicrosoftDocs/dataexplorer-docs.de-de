---
title: Erstellen von BCDR-Lösungen (Business Continuity & Disaster Recovery) mit Azure Data Explorer
description: In diesem Artikel erfahren Sie, wie Sie BCDR-Lösungen (Business Continuity & Disaster Recovery) mit Azure Data Explorer erstellen.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 0b37d65d095806d108a632c315820d164f45a520
ms.sourcegitcommit: 39a055e246539ac64d651abb42531892dd4393e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88028903"
---
# <a name="create-business-continuity-and-disaster-recovery-solutions-with-azure-data-explorer"></a>Erstellen von BCDR-Lösungen (Business Continuity & Disaster Recovery) mit Azure Data Explorer

In diesem Artikel erfahren Sie, wie Sie sich auf einen regionalen Azure-Ausfall vorbereiten können, indem Sie Ihre Azure Data Explorer-Ressourcen, Ihre Verwaltung und Ihre Erfassung in verschiedenen Azure-Regionen replizieren. Der Artikel enthält auch ein Beispiel für die Datenerfassung mit Event Hub. Außerdem wird auf die Kostenoptimierung bei verschiedenen Architekturkonfigurationen eingegangen. Eine ausführlichere Betrachtung von Architekturaspekten und Wiederherstellungslösungen finden Sie in der [Übersicht über die Geschäftskontinuität](business-continuity-overview.md).

## <a name="prepare-for-azure-regional-outage-to-protect-your-data"></a>Vorbereiten auf einen regionalen Azure-Ausfall zum Schutz Ihrer Daten

Azure Data Explorer bietet keinen automatischen Schutz vor dem Ausfall einer gesamten Azure-Region. Eine solche Störung kann durch eine Naturkatastrophe (beispielsweise ein Erdbeben) verursacht werden. Sollten Sie eine Notfallwiederherstellungslösung benötigen, führen Sie die folgenden Schritte aus, um die Geschäftskontinuität zu gewährleisten. In diesen Schritten werden Ihre Cluster, Ihre Verwaltung und Ihre Datenerfassung in zwei Azure-Regionspaaren repliziert.

1. [Erstellen Sie mindestens zwei unabhängige Cluster](#create-multiple-independent-clusters) in zwei Azure-Regionspaaren.
1. [Replizieren Sie alle Verwaltungsaktivitäten](#replicate-management-activities) wie etwa die Erstellung neuer Tabellen oder die Verwaltung von Benutzerrollen in jedem Cluster.
1. Erfassen Sie Daten parallel in jedem Cluster.

### <a name="create-multiple-independent-clusters"></a>Erstellen mehrerer unabhängiger Cluster

Erstellen Sie mehrere [Azure Data Explorer-Cluster](create-cluster-database-portal.md) in mehreren Regionen.
Mindestens zwei dieser Cluster müssen in [Azure-Regionspaaren](/azure/best-practices-availability-paired-regions) erstellt werden.

Die folgende Abbildung zeigt Replikate. Hierbei handelt es sich um drei Cluster in drei verschiedenen Regionen: 

:::image type="content" source="media/business-continuity-create-solution/independent-clusters.png" alt-text="Erstellen unabhängiger Cluster":::

### <a name="replicate-management-activities"></a>Replizieren von Verwaltungsaktivitäten

Replizieren Sie die Verwaltungsaktivitäten so, dass in jedem Replikat die gleiche Clusterkonfiguration vorhanden ist.

1. Erstellen Sie in den einzelnen Replikaten jeweils die gleichen: 
    * Datenbanken: Neue Datenbanken können über das [Azure-Portal](create-cluster-database-portal.md#create-a-database) oder mithilfe eines unserer [SDKs](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/kusto/Microsoft.Azure.Management.Kusto) erstellt werden.
    * [Tabellen](kusto/management/create-table-command.md)
    * [Zuordnungen](kusto/management/create-ingestion-mapping-command.md)
    * [Richtlinien](kusto/management/policies.md)

1. Verwalten Sie die [Authentifizierung und Autorisierung](kusto/management/security-roles.md) für jedes Replikat.

    :::image type="content" source="media/business-continuity-create-solution/regional-duplicate-management.png" alt-text="Duplizieren von Verwaltungsaktivitäten":::    

### <a name="configure-data-ingestion"></a>Konfigurieren der Datenerfassung

Konfigurieren Sie die Datenerfassung konsistent in jedem Cluster. Von den folgenden Erfassungsmethoden werden die folgenden erweiterten Geschäftskontinuitätsfeatures verwendet:

|Erfassungsmethode  |Notfallwiederherstellungsfeature  |
|---------|---------|
|[IoT Hub](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr)  |[Von Microsoft initiiertes Failover und manuelles Failover](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr) |
|[Event Hub](ingest-data-event-hub.md) | Notfallwiederherstellung von Metadaten mithilfe [primärer und sekundärer Namespaces für die Notfallwiederherstellung](/azure/event-hubs/event-hubs-geo-dr)     |
|[Erfassung aus dem Speicher mithilfe eines Event Grid-Abonnements](ingest-data-event-grid.md) |  Implementieren einer [georedundanten Notfallwiederherstellung](/azure/event-hubs/event-hubs-geo-dr) für durch Blobs erstellte Nachrichten, die an den Event Hub gesendet werden, und Implementieren der Strategie für [Notfallwiederherstellung und Speicherkontofailover](/azure/storage/common/storage-disaster-recovery-guidance)       |

## <a name="disaster-recovery-solution-using-event-hub-ingestion"></a>Notfallwiederherstellungslösung mit Event Hub-Erfassung

Nachdem Sie die Schritte unter [Vorbereiten auf einen regionalen Azure-Ausfall zum Schutz Ihrer Daten](#prepare-for-azure-regional-outage-to-protect-your-data) ausgeführt haben, sind Ihre Daten und Ihre Verwaltung auf mehrere Regionen verteilt. Sollte eine Region ausfallen, kann Azure Data Explorer auf die anderen Replikate ausweichen. 

### <a name="set-up-ingestion-using-event-hub"></a>Einrichten der Erfassung mit Event Hub

Im folgenden Beispiel wird eine Erfassung über Event Hub verwendet. Ein [Failoverablauf](/azure/event-hubs/event-hubs-geo-dr#setup-and-failover-flow) wurde eingerichtet, und von Azure Data Explorer werden Daten aus dem Alias erfasst. [Erfassen Sie Daten aus dem Event Hub](ingest-data-event-hub.md) unter Verwendung einer eindeutigen Consumergruppe pro Clusterreplikat. Andernfalls wird der Datenverkehr nicht repliziert, sondern verteilt.

> [!NOTE] 
> Die Erfassung über Event Hub/IoT Hub/Speicher ist robust. Sollte ein Cluster vorübergehend nicht verfügbar sein, wird er später auf den neuesten Stand gebracht, und ggf. ausstehenden Nachrichten oder Blobs werden eingefügt. Dieser Prozess basiert auf dem [Setzen von Prüfpunkten](/azure/event-hubs/event-hubs-features#checkpointing).

:::image type="content" source="media/business-continuity-create-solution/event-hub-management-scheme.png" alt-text="Erfassen über Event Hub":::

Wie im folgenden Diagramm zu sehen, werden von Ihren Datenquellen Ereignisse für den Event Hub mit Failoverkonfiguration generiert und von den einzelnen Azure Data Explorer-Replikaten genutzt. Von Datenvisualisierungskomponenten wie Power BI, Grafana oder SDK-gestützten Web-Apps können Abfragen an eines der Replikate gesendet werden.

:::image type="content" source="media/business-continuity-create-solution/data-sources-visualization.png" alt-text="Datenquellen und Datenvisualisierung":::

## <a name="optimize-costs"></a>Optimieren der Kosten

Nun können Sie Ihre Replikate mithilfe einiger der folgenden Methoden optimieren:

* [Erstellen einer Konfiguration mit einem aktiven und einem unmittelbar betriebsbereiten Cluster](#create-an-active-hot-standby-configuration)
* [Starten und Beenden der Replikate](#start-and-stop-the-replicas)
* [Implementieren eines hochverfügbaren Anwendungsdiensts](#implement-a-highly-available-application-service)
* [Optimieren der Kosten in einer Aktiv/Aktiv-Konfiguration](#optimize-cost-in-an-active-active-configuration)

### <a name="create-an-active-hot-standby-configuration"></a>Erstellen einer Konfiguration mit einem aktiven und einem unmittelbar betriebsbereiten Cluster

Durch die Replizierung und Aktualisierung des Azure Data Explorer-Setups nehmen die Kosten linear zur Replikatanzahl zu. Zur Kostenoptimierung können Sie eine Architekturvariante implementieren, um ein ausgewogenes Verhältnis zwischen Zeit, Failover und Kosten zu erreichen.
In einer Konfiguration mit einem aktiven und einem unmittelbar betriebsbereiten Cluster wurde die Kostenoptimierung durch die Einführung passiver Azure Data Explorer-Replikate implementiert. Diese Replikate werden nur aktiviert, wenn in der primären Region (beispielsweise in der Region A) ein Notfall vorliegt. Die Replikate in den Regionen B und C müssen nicht rund um die Uhr aktiv sein, wodurch die Kosten erheblich sinken. In den meisten Fällen ist die Leistung dieser Replikate allerdings nicht so gut wie die des der primären Clusters. Weitere Informationen finden Sie unter [Konfiguration mit einem aktiven und einem unmittelbar betriebsbereiten Cluster](business-continuity-overview.md#active-hot-standby-configuration).

In der folgenden Abbildung erfasst nur ein einzelner Cluster Daten aus dem Event Hub. Vom primären Cluster in der Region A werden alle Daten mittels [kontinuierlichem Datenexport](kusto/management/data-export/continuous-data-export.md) in ein Speicherkonto exportiert. Von den sekundären Replikaten kann über [externe Tabellen](kusto/query/schema-entities/externaltables.md) auf die Daten zugegriffen werden.

:::image type="content" source="media/business-continuity-create-solution/active-hot-standby-scheme.png" alt-text="Architektur für einen aktiven und einen unmittelbar betriebsbereiten Cluster":::

### <a name="start-and-stop-the-replicas"></a>Starten und Beenden der Replikate 

Die sekundären Replikate können mithilfe einer der folgenden Methoden gestartet und beendet werden:

* [Azure Data Explorer-Connector für Power Automate (Vorschauversion)](flow.md)

* Schaltfläche **Beenden** auf der Registerkarte **Übersicht** des Azure-Portals. Weitere Informationen finden Sie unter [Beenden und Neustarten des Clusters](create-cluster-database-portal.md#stop-and-restart-the-cluster).

* Azure CLI: 

```kusto
az kusto cluster stop --name=<clusterName> --resource-group=<rgName> --subscription=<subscriptionId>” 
```

### <a name="implement-a-highly-available-application-service"></a>Implementieren eines hochverfügbaren Anwendungsdiensts

#### <a name="create-the-azure-app-service-bcdr-client"></a>Erstellen des Azure App Service-BCDR-Clients

In diesem Abschnitt erfahren Sie, wie Sie eine Instanz von [Azure App Service](https://azure.microsoft.com/services/app-service/) erstellen, die eine Verbindung mit einem einzelnen primären und mehreren sekundären Azure Data Explorer-Clustern unterstützt. In der folgenden Abbildung sehen Sie das Azure App Service-Setup:

:::image type="content" source="media/business-continuity-create-solution/app-service-setup.png" alt-text="Erstellen von Azure App Service":::

> [!TIP]
> Durch mehrere Verbindungen zwischen Replikaten im gleichen Dienst erhöht sich die Verfügbarkeit. Dieses Setup ist nicht nur bei regionalen Ausfällen hilfreich.  

1. Verwenden Sie diese [Codebausteine für einen App-Dienst](https://github.com/Azure/azure-kusto-bcdr-boilerplate). Für die Implementierung eines Multiclusterclients wurde die Klasse [AdxBcdrClient](https://github.com/Azure/azure-kusto-bcdr-boilerplate/blob/master/webapp/ADX/AdxBcdrClient.cs) erstellt. Jede Abfrage, die mithilfe dieses Clients ausgeführt wird, wird [zuerst an den primären Cluster](https://github.com/Azure/azure-kusto-bcdr-boilerplate/blob/26f8c092982cb8a3757761217627c0e94928ee07/webapp/ADX/AdxBcdrClient.cs#L69) gesendet. Im Falle eines Ausfalls wird die Abfrage an sekundäre Replikate gesendet.

1. Verwenden Sie [benutzerdefinierte Application Insights-Metriken](/azure/azure-monitor/app/api-custom-events-metrics), um die Leistung zu messen, und fordern Sie die Verteilung auf primäre und sekundäre Cluster an. 

#### <a name="test-the-azure-app-service-bcdr-client"></a>Testen des Azure App Service-BCDR-Clients

Wir haben einen Test mit mehreren Azure Data Explorer-Replikaten durchgeführt. Wie Sie sehen, verhält sich der App Service-BCDR-Client nach einem simulierten Ausfall primärer und sekundärer Cluster wie vorgesehen.

:::image type="content" source="media/business-continuity-create-solution/simulation-verify-service.png" alt-text="Überprüfen des App Service-BCDR-Clients":::

Die Azure Data Explorer-Cluster sind auf „Europa, Westen“ (2x D14v2, primär), „Asien, Südosten“ und „USA, Osten“ (2x D11v2) verteilt. 

:::image type="content" source="media/business-continuity-create-solution/performance-test-query-time.png" alt-text="Antwortzeit für weltweite Abfragen":::

> [!NOTE]
> Langsamere Antwortzeiten sind auf unterschiedliche SKUs und weltweite Abfragen zurückzuführen.

#### <a name="perform-dynamic-or-static-routing"></a>Ausführen von dynamischem oder statischem Routing

Verwenden Sie [Traffic Manager-Routingmethoden](/azure/traffic-manager/traffic-manager-routing-methods) für dynamisches oder statisches Anforderungsrouting.  Azure Traffic Manager ist ein DNS-basierter Lastenausgleichsdienst, mit dem Sie App Service-Datenverkehr verteilen können. Dieser Datenverkehr wird für Dienste in Azure-Regionen auf der ganzen Welt optimiert. Gleichzeitig profitieren Sie von Hochverfügbarkeit und hoher Reaktionsfähigkeit. 

Sie können auch [Azure Front Door-basiertes Routing](/azure/frontdoor/front-door-routing-methods) verwenden. Eine Gegenüberstellung dieser beiden Methoden finden Sie unter [Lastenausgleich mit der Azure-Suite für die Anwendungsbereitstellung](/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).

### <a name="optimize-cost-in-an-active-active-configuration"></a>Optimieren der Kosten in einer Aktiv/Aktiv-Konfiguration

Bei Verwendung einer Aktiv/Aktiv-Konfiguration für die Notfallwiederherstellung steigen die Kosten linear an. Darin enthalten sind die Kosten für Knoten, Speicher und Markup sowie höhere Netzwerkkosten für die [Bandbreite](https://azure.microsoft.com/pricing/details/bandwidth/).

#### <a name="use-optimized-autoscale-to-optimize-costs"></a>Optimieren der Kosten durch optimierte Autoskalierung

Verwenden Sie das Feature [Optimierte Autoskalierung](manage-cluster-horizontal-scaling.md#optimized-autoscale), um die horizontale Skalierung für die sekundären Cluster zu konfigurieren. Diese sollten so dimensioniert sein, dass sie die Erfassungslast bewältigen können. Ist der primäre Cluster nicht erreichbar, erhöht sich der Datenverkehr für die sekundären Cluster, und die Cluster werden konfigurationsgemäß skaliert. 

Durch die Verwendung der optimierten Autoskalierung konnten die Kosten in diesem Beispiel ungefähr halbiert werden (verglichen mit der Verwendung der gleichen horizontalen und vertikalen Skalierung für alle Replikate).

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich zum Einstieg die [Übersicht über Business Continuity & Disaster Recovery](business-continuity-overview.md) an.
