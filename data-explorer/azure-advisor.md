---
title: Optimieren Ihres Azure Data Explorer-Clusters mithilfe von Azure Advisor-Empfehlungen
description: In diesem Artikel werden Azure Advisor-Empfehlungen beschrieben, mit deren Hilfe Sie Ihren Azure Data Explorer-Cluster optimieren können.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: lizlotor
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/14/2020
ms.openlocfilehash: f2439097258ea6c8b4780bf04dd358d47b42d480
ms.sourcegitcommit: 483f6902735f399bf889035cbc41ddb68d1a71b8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102189185"
---
# <a name="use-azure-advisor-recommendations-to-optimize-your-azure-data-explorer-cluster-preview"></a>Optimieren Ihres Azure Data Explorer-Clusters mithilfe von Azure Advisor-Empfehlungen (Vorschau)

Azure Advisor analysiert die Konfigurationen und Nutzungstelemetriedaten Ihrer Azure Data Explorer-Cluster und bietet personalisierte und handlungsrelevante Empfehlungen, die Ihnen bei der Optimierung von Clustern helfen.

## <a name="access-the-azure-advisor-recommendations"></a>Zugreifen auf die Azure Advisor-Empfehlungen

Es gibt zwei Möglichkeiten, auf die Azure Advisor-Empfehlungen zuzugreifen.

### <a name="view-azure-advisor-recommendations-for-your-azure-data-explorer-cluster"></a>Anzeigen von Azure Advisor-Empfehlungen für Ihren Azure Data Explorer-Cluster

1. Navigieren Sie im Azure-Portal zur Seite mit dem Azure Data Explorer-Cluster. 
1. Wählen Sie im Menü auf der linken Seite unter **Überwachung** die Option **Advisor-Empfehlungen** aus. Eine Liste der Empfehlungen für diesen Cluster wird geöffnet.

    :::image type="content" source="media/azure-advisor/resource-group-advisor-recommendations.png" alt-text="Azure Advisor-Empfehlungen für Ihren Azure Data Explorer-Cluster"::: 

### <a name="view-azure-advisor-recommendations-for-all-clusters-in-your-subscription"></a>Anzeigen von Azure Advisor-Empfehlungen für alle Cluster in Ihrem Abonnement

1. Navigieren Sie im Azure-Portal zur [Advisor-Ressource](https://ms.portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview). 
1. Wählen Sie unter **Übersicht** die Abonnements aus, für die Sie Empfehlungen erhalten möchten. 
1. Wählen Sie **Azure Data Explorer-Cluster** und im zweiten Dropdownmenü **Azure Data Explorer-Datenbanken** aus.
 
    :::image type="content" source="media/azure-advisor/advisor-resource.png" alt-text="Azure Advisor-Ressource":::

## <a name="use-the-azure-advisor-recommendations"></a>Verwenden der Azure Advisor-Empfehlungen

Es gibt verschiedene Azure Advisor-Empfehlungstypen. Verwenden Sie den entsprechenden Empfehlungstyp für die Clusteroptimierung. 

1. Wählen Sie in **Advisor** unter **Empfehlungen** die Option **Kosten** aus, um Kostenempfehlungen zu erhalten.

    :::image type="content" source="media/azure-advisor/select-recommendation-type.png" alt-text="Auswählen des Empfehlungstyps":::

1. Wählen Sie in der Liste eine Empfehlung aus. 

    :::image type="content" source="media/azure-advisor/select-recommendation.png" alt-text="Auswählen einer Empfehlung":::

1. Das folgende Fenster enthält eine Liste von Clustern, für die die Empfehlung relevant ist. Die Empfehlungsdetails unterscheiden sich für jeden Cluster und enthalten die empfohlene Aktion.

    :::image type="content" source="media/azure-advisor/clusters-with-recommendations.png" alt-text="Liste der Cluster mit Empfehlungen":::

## <a name="recommendation-types"></a>Empfehlungstypen

Derzeit sind Kosten- und Leistungsempfehlungen verfügbar.

> [!IMPORTANT]
> Ihre tatsächlichen jährlichen Einsparungen können davon abweichen. Die angegebenen jährlichen Einsparungen basieren auf den Preisen für die nutzungsbasierte Zahlung. Bei den potenziellen Einsparungen werden Abrechnungsrabatte für reservierte Azure-VM-Instanzen (RIs) nicht berücksichtigt.

### <a name="cost-recommendations"></a>Kostenempfehlungen

Die **Kostenempfehlungen** sind für Cluster verfügbar, die geändert werden können, um Kosten zu senken, ohne die Leistung zu beeinträchtigen. Zu den Kostenempfehlungen zählen u. a. folgende: 

* [Nicht verwendeter Azure Data Explorer-Cluster](#azure-data-explorer-unused-cluster)
* [Azure Data Explorer-Cluster mit Daten und geringer Aktivität](#azure-data-explorer-clusters-containing-data-with-low-activity)
* [Richtiges Dimensionieren des Azure Data Explorer-Clusters zur Optimierung der Kosten](#correctly-size-azure-data-explorer-clusters-to-optimize-cost)
* [Verringern des Caches für Azure Data Explorer-Tabellen](#reduce-cache-for-azure-data-explorer-tables)
* [Ausführen eines Bereinigungsbefehls zum Löschen von nicht verwendeten Speicherartefakten](#delete-unused-storage-artifacts)

#### <a name="azure-data-explorer-unused-cluster"></a>Nicht verwendeter Azure Data Explorer-Cluster

Ein Cluster wird als nicht verwendet betrachtet, wenn er in den letzten 30 Tagen nur eine geringe Menge an Daten, Abfragen und Erfassungsereignissen verarbeitet hat, während der letzten zwei Tage die CPU-Auslastung gering war und es keine Follower während des letzten Tages gegeben hat. Die Empfehlung, **die Löschung leerer/nicht verwendeter Cluster zu erwägen**, enthält die empfohlene Aktion zum Löschen des nicht verwendeten Clusters.

#### <a name="azure-data-explorer-clusters-containing-data-with-low-activity"></a>Azure Data Explorer-Cluster mit Daten und geringer Aktivität

Die Empfehlung, **Azure Data Explorer-Cluster zu beenden, um Kosten zu senken und Daten beizubehalten**, wird für einen Cluster angegeben, der Daten enthält, aber geringe Aktivität aufweist. Geringe Aktivität bedeutet eine geringe Menge an Abfragen und Erfassungen in den letzten 30 Tagen, eine geringe CPU-Auslastung während der letzten zwei Tage und keine Follower während des letzten Tages. Es wird empfohlen, den Cluster zu beenden, um die Kosten zu senken, die Daten jedoch beizubehalten. Werden die Daten nicht benötigt, sollten Sie den Cluster löschen, um die Einsparungen zu erhöhen.

#### <a name="correctly-size-azure-data-explorer-clusters-to-optimize-cost"></a>Richtiges Dimensionieren des Azure Data Explorer-Clusters zur Optimierung der Kosten

Die Empfehlung zum **richtigen Dimensionieren von Azure Data Explorer-Clustern zur Kostenoptimierung** wird für einen Cluster angezeigt, dessen Größe oder VM-SKU nicht kostenoptimiert ist. Diese Empfehlung basiert auf Parametern, etwa der Datenkapazität oder der CPU- und Erfassungsauslastung während der letzten Woche. Sie können die Kosten senken, indem Sie die Größe mithilfe des [zentralen Herunterskalierens](manage-cluster-vertical-scaling.md) und des [horizontalen Herunterskalierens](manage-cluster-horizontal-scaling.md) auf die empfohlene Clusterkonfiguration festlegen.

Es wird empfohlen, die [Konfiguration für die optimierte Autoskalierung](manage-cluster-horizontal-scaling.md#optimized-autoscale) zu verwenden. Wenn Sie die optimierte Autoskalierung verwenden und die Größenempfehlung für Ihren Cluster angezeigt wird, ist entweder die aktuelle VM-SKU nicht optimiert oder die minimalen und maximalen Grenzen für die Anzahl der Instanzen für die optimierte Autoskalierung sind nicht optimiert. Die empfohlene Anzahl der Instanzen sollte in den definierten Grenzen enthalten sein. Weitere Informationen finden Sie unter [Auswählen der passenden Compute-SKU für Ihren Azure Data Explorer-Cluster](manage-cluster-choose-sku.md) und [Azure-Daten-Explorer – Preise](https://azure.microsoft.com/pricing/details/data-explorer/).

> [!TIP]
> Die Konfiguration für die optimierte Autoskalierung ändert die Anzahl der Instanzen nicht sofort. Verwenden Sie für sofortige Änderungen die [manuelle Skalierung](manage-cluster-horizontal-scaling.md#manual-scale), um die empfohlene Instanzanzahl zurückzusetzen, und aktivieren Sie dann die optimierte Autoskalierung für die zukünftige Optimierung.

#### <a name="reduce-cache-for-azure-data-explorer-tables"></a>Verringern des Caches für Azure Data Explorer-Tabellen

Die Empfehlung zum **Verkürzen des Azure Data Explorer-Tabellencachezeitraums zur Clusterkostenoptimierung** wird für einen Cluster angegeben, bei dem die [Cacherichtlinie](kusto/management/cachepolicy.md) der Tabelle reduziert werden kann. Diese Empfehlung basiert auf dem Abfragezeitraum der letzten 30 Tage. Die zehn obersten Tabellen mit möglichen Cacheeinsparungen werden angezeigt. Diese Empfehlung wird nur angeboten, wenn der Cluster nach der Änderung der Cacherichtlinie horizontal oder zentral herunterskaliert werden kann. Advisor überprüft, ob der Cluster „durch Daten begrenzt“ ist. Das bedeutet, dass der Cluster eine geringe CPU- und Erfassungsauslastung aufweist, aufgrund einer hohen Datenkapazität jedoch nicht zentral oder horizontal herunterskaliert werden konnte.

#### <a name="delete-unused-storage-artifacts"></a>Löschen von nicht verwendeten Speicherartefakten

Die Empfehlung zum **Löschen nicht verwendeter Speicherartefakte** wird für einen Cluster angegeben, der nicht verwendete Speicherartefakte aus Wartungs- und Hintergrundvorgängen für [Datenshards (Blöcke)](kusto/management/extents-overview.md) enthält. Im Laufe der Zeit können sich bei internen Zusammenführungsvorgängen für Blöcke redundante und nicht verwendete Speicherartefakte ansammeln, die über den Datenaufbewahrungszeitraum hinaus erhalten bleiben. Diese nicht referenzierten Daten wirken sich zwar nicht negativ auf die Leistung aus, können aber zu einer höheren Speichernutzung als nötig führen.
Es wird empfohlen, den Befehl [clean databases extentcontainers](kusto/management/clean-extent-containers.md#clean-databases-extentcontainers) auszuführen, um nicht verwendete Speicherartefakte zu ermitteln und zu löschen und dadurch Kosten zu senken. 

> [!IMPORTANT]
> Die Wiederherstellbarkeit von Daten wird auf die Bereinigungszeit zurückgesetzt, und Daten, die vor der Bereinigung erstellt wurden, können nicht wiederhergestellt werden.

### <a name="performance-recommendations"></a>Empfehlungen zur Leistung

Die **Leistungsempfehlungen** tragen zur Verbesserung der Leistung Ihrer Azure Data Explorer-Cluster bei. Zu den Leistungsempfehlungen zählen u. a. folgende: 
* [Richtiges Dimensionieren des Azure Data Explorer-Clusters zur Optimierung der Leistung](#correctly-size-azure-data-explorer-clusters-to-optimize-performance)
* [Aktualisieren der Cacherichtlinie für Azure Data Explorer-Tabellen](#update-cache-policy-for-azure-data-explorer-tables)

#### <a name="correctly-size-azure-data-explorer-clusters-to-optimize-performance"></a>Richtiges Dimensionieren des Azure Data Explorer-Clusters zur Optimierung der Leistung

Die Empfehlung zum **richtigen Dimensionieren von Azure Data Explorer-Clustern zur Leistungsoptimierung** wird für einen Cluster angezeigt, dessen Größe oder VM-SKU nicht leistungsoptimiert ist. Diese Empfehlung basiert auf Parametern, etwa der Datenkapazität oder der CPU- und Erfassungsauslastung während der letzten Woche. Sie können die Leistung verbessern, indem Sie die Größe mithilfe des [zentralen Hochskalierens](manage-cluster-vertical-scaling.md) und der [horizontalen Skalierung](manage-cluster-horizontal-scaling.md) auf die empfohlene Clusterkonfiguration festlegen.

Es wird empfohlen, die [Konfiguration für die optimierte Autoskalierung](manage-cluster-horizontal-scaling.md#optimized-autoscale) zu verwenden. Wenn Sie die optimierte Autoskalierung verwenden und die Größenempfehlung für Ihren Cluster angezeigt wird, ist entweder die aktuelle VM-SKU nicht optimiert oder die minimalen und maximalen Grenzen für die Anzahl der Instanzen für die optimierte Autoskalierung sind nicht optimiert. Die empfohlene Anzahl der Instanzen sollte in den definierten Grenzen enthalten sein. Weitere Informationen finden Sie unter [Auswählen der passenden Compute-SKU für Ihren Azure Data Explorer-Cluster](manage-cluster-choose-sku.md).

> [!TIP]
> Die Konfiguration für die optimierte Autoskalierung ändert die Anzahl der Instanzen nicht sofort. Verwenden Sie für sofortige Änderungen die [manuelle Skalierung](manage-cluster-horizontal-scaling.md#manual-scale), um die empfohlene Instanzanzahl zurückzusetzen, und aktivieren Sie dann die optimierte Autoskalierung für die zukünftige Optimierung.

#### <a name="update-cache-policy-for-azure-data-explorer-tables"></a>Aktualisieren der Cacherichtlinie für Azure Data Explorer-Tabellen

Die Empfehlung zum **Überprüfen des Cachezeitraums von Azure Data Explorer-Tabellen (Richtlinie) zur Leistungsverbesserung** wird für einen Cluster angezeigt, für den ein anderer Rückblickzeitraum (Zeitfilter) oder eine größere [Cacherichtlinie](kusto/management/cachepolicy.md) erforderlich ist. Diese Empfehlung basiert auf dem Abfragezeitraum der letzten 30 Tage. Die meisten Abfragen, die in den letzten 30 Tagen ausgeführt wurden, haben auf Daten zugegriffen, die nicht im Cache waren. Dadurch kann sich die Abfragelaufzeit erhöhen.  Die obersten zehn Tabellen werden nach Abfrageprozentsatz sortiert angezeigt, von denen auf Daten außerhalb des Caches zugegriffen wird.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten des horizontalen Skalierens (Aufskalieren) eines Clusters in Azure Data Explorer zur Anpassung an sich ändernden Bedarf](manage-cluster-horizontal-scaling.md)
* [Verwalten des vertikalen Hoch- oder Herunterskalierens eines Clusters in Azure Data Explorer, um ihn an den sich ändernden Bedarf anzupassen](manage-cluster-vertical-scaling.md)
