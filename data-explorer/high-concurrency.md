---
title: Optimieren für hohe Parallelität mit Azure Data Explorer
description: In diesem Artikel erfahren Sie, wie Sie Ihr Azure Data Explorer-Setup für hohe Parallelität optimieren.
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 2fd45c2608e83c46b39d65fd8d3d564ca1b2df29
ms.sourcegitcommit: 2b0156fc244757e62aaef5d4782c4abebaf09754
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99808686"
---
# <a name="optimize-for-high-concurrency-with-azure-data-explorer"></a>Optimieren für hohe Parallelität mit Azure Data Explorer

Anwendungen mit hoher Parallelität werden in Szenarien mit einer großen Benutzerbasis benötigt, in denen die Anwendung gleichzeitig zahlreiche Anforderungen mit geringer Wartezeit und hohem Durchsatz verarbeiten muss. 

Anwendungsfälle wären etwa umfangreiche Überwachungs- und Warnungsdashboards (beispielsweise Microsoft-Produkte und -Dienste wie [Azure Monitor](https://azure.microsoft.com/services/monitor/), [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) und [PlayFab](https://playfab.com/)). Von allen diesen Diensten wird Azure Data Explorer verwendet, um Workloads mit hoher Parallelität bereitzustellen. Azure Data Explorer ist ein schneller, vollständig verwalteter Big Data-Analysedienst für die Echtzeitanalyse großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten und Ähnlichem gestreamt werden. 

> [!NOTE]
> Die tatsächliche Anzahl von Abfragen, die gleichzeitig in einem Cluster ausgeführt werden können, hängt von verschiedenen Faktoren wie Clusterressourcen, Datenvolumen, Abfragekomplexität und Nutzungsmustern ab.

Gestalten Sie die Back-End-Architektur wie folgt, um sie für Anwendungen mit hoher Parallelität einzurichten:

* [Optimieren von Daten](#optimize-data)
* [Festlegen des Leader-Follower-Architekturmusters](#set-leader-follower-architecture-pattern)
* [Optimieren von Abfragen](#optimize-queries)
* [Festlegen von Clusterrichtlinien](#set-cluster-policies)
* [Überwachen von Azure Data Explorer-Clustern](#monitor-azure-data-explorer-clusters)

Dieser Artikel enthält diverse Empfehlungen zu jedem der obigen Themen, die Sie implementieren können, um auf optimale und kostengünstige Weise eine hohe Parallelität zu erreichen. Diese Features können einzeln verwendet oder miteinander kombiniert werden.


## <a name="optimize-data"></a>Optimieren von Daten

Abfragen sollten möglichst wenig CPU-Ressourcen beanspruchen, um eine hohe Parallelität zu erreichen. Sie können eine beliebige oder alle der folgenden Methoden verwenden: [optimiertes Design des Tabellenschemas](#use-table-schema-design-best-practices), [Datenpartitionierung](#partition-data), [Vorabaggregation](#pre-aggregate-your-data-with-materialized-views) und [Zwischenspeichern](#configure-caching-policy).

### <a name="use-table-schema-design-best-practices"></a>Verwenden bewährter Methoden für das Design des Tabellenschemas

Nutzen Sie die folgenden Designvorschläge für das Tabellenschema, um die CPU-Ressourcenauslastung zu minimieren:

* Stimmen Sie den Spaltendatentyp optimal auf die Daten ab, die tatsächlich in den Spalten gespeichert sind. Speichern Sie beispielsweise Datums-/Uhrzeitwerte nicht in einer Zeichenfolgenspalte.
* Vermeiden Sie große Tabellen mit geringer Dichte sowie die Verwendung dynamischer Spalten zum Speichern von Eigenschaften mit geringer Dichte.
* Speichern Sie häufig verwendete Eigenschaften in einer eigenen Spalte mit einem nicht dynamischen Datentyp.
* Denormalisieren Sie Daten, um Verknüpfungen zu vermeiden, die mit einem relativ hohen CPU-Ressourcenbedarf einhergehen.

### <a name="partition-data"></a>Partitionieren von Daten

Daten werden in Form von horizontalen Datenbankpartitionen (Datenshards) gespeichert und standardmäßig nach Erfassungszeit partitioniert. Mithilfe der [Partitionierungsrichtlinie](kusto/management/partitioningpolicy.md) können Sie die horizontalen Datenbankpartitionen in einem Hintergrundprozess auf der Grundlage einer einzelnen Zeichenfolgen- oder Datums-/Uhrzeitspalte neu partitionieren. Durch die Partitionierung lassen sich mitunter erhebliche Leistungsverbesserungen erzielen, wenn die meisten Abfragen Partitionsschlüssel zum Filtern oder Aggregieren (oder für beides) verwenden.

> [!NOTE]
> Beim eigentlichen Partitionierungsprozess werden CPU-Ressourcen beansprucht. Die Verringerung der CPU-Auslastung zur Abfragezeit sollte jedoch die CPU-Auslastung für die Partitionierung aufwiegen.

### <a name="pre-aggregate-your-data-with-materialized-views"></a>Vorabaggregieren Ihrer Daten mit materialisierten Sichten

Aggregieren Sie Ihre Daten vorab, um die zur Abfragezeit beanspruchten CPU-Ressourcen erheblich zu verringern. Beispiele für Szenarien wären etwa das Zusammenfassen von Datenpunkten über eine geringere Anzahl von Zeitabschnitten, das Speichern des aktuellen Datensatzes eines bestimmten Datensatzes oder das Deduplizieren des Datasets. Verwenden Sie [materialisierte Sichten](kusto/management/materialized-views/materialized-view-overview.md), um eine einfach zu konfigurierende aggregierte Sicht für Quelltabellen zu erhalten. Dieses Feature vereinfacht das Erstellen und Verwalten dieser aggregierten Sichten.

> [!NOTE]
> Durch den Aggregationsprozess im Hintergrund werden CPU-Ressourcen beansprucht. Die Verringerung der CPU-Auslastung zur Abfragezeit sollte jedoch die CPU-Auslastung für die Aggregation aufwiegen.

### <a name="configure-caching-policy"></a>Konfigurieren der Cacherichtlinie

Konfigurieren Sie die Cacherichtlinie so, dass Abfragen auf der Grundlage von Daten ausgeführt werden, die im „heißen“ Speicher (Datenträgercache) gespeichert sind. Verwenden Sie Cold Storage oder externe Tabellen nur in wenigen, sorgfältig entworfenen Szenarien.

## <a name="set-leader-follower-architecture-pattern"></a>Festlegen des Leader-Follower-Architekturmusters

Bei der Follower-Datenbank handelt es sich um ein Feature, das einer Datenbank oder einer Gruppe von Tabellen in einer Datenbank aus einem anderen Cluster in der gleichen Region folgt. Dieses Feature wird über [Azure Data Share](/azure/data-explorer/data-share), [Azure Resource Manager-APIs](follower.md) und eine Reihe von [Clusterbefehlen](kusto/management/cluster-follower.md) verfügbar gemacht. 

Verwenden Sie das Leader-Follower-Muster, um Computeressourcen für verschiedene Workloads festzulegen. Richten Sie beispielsweise einen Cluster für Erfassungen, einen Cluster für Abfragen oder für die Bereitstellung von Dashboards oder Anwendungen und einen Cluster für die Bereitstellung der Data Science-Workloads ein. Alle Workloads verfügen in diesem Fall jeweils über dedizierte Computeressourcen, die unabhängig voneinander skaliert werden können, sowie über unterschiedliche Cache- und Sicherheitskonfigurationen. Von allen Clustern werden die gleichen Daten verwendet. Die Daten werden dabei vom Leader geschrieben und von den Followern im schreibgeschützten Modus genutzt.

> [!NOTE]
> Die Verzögerung zwischen Follower-Datenbanken und Leader beträgt in der Regel einige Minuten. Sollte Ihre Lösung auf neueste Daten ohne Wartezeit angewiesen sein, ist möglicherweise die folgende Lösung hilfreich: Verwenden Sie eine Sicht für den Follower-Cluster, die die Daten des Leaders mit den Daten des Followers vereint, indem die neuesten Daten vom Leader und die restlichen Daten vom Follower abgefragt werden.

Die Leistung von Abfragen für den Follower-Cluster lässt sich durch Aktivieren der [Konfiguration zum Vorabruf von horizontalen Datenbankpartitionen](kusto/management/cluster-follower.md#alter-follower-database-prefetch-extents) verbessern. Verwenden Sie diese Konfiguration jedoch mit Bedacht, da sie sich auf die Aktualität der Daten in der Follower-Datenbank auswirken kann.

## <a name="optimize-queries"></a>Optimieren von Abfragen

Verwenden Sie die folgenden Methoden, um Ihre Abfragen für hohe Parallelität zu optimieren:

### <a name="use-query-results-cache"></a>Verwenden des Abfrageergebniscaches

Wenn mehrere Benutzer zu einer ähnlichen Zeit das gleiche Dashboard laden, kann das Dashboard für den zweiten und die weiteren Benutzer aus dem Cache bereitgestellt werden. Dieses Setup bietet eine hohe Leistung und beansprucht fast keine CPU-Ressourcen. Verwenden Sie das Feature [Abfrageergebniscache](kusto/query/query-results-cache.md) und die Anweisung `set`, um die Konfiguration des Abfrageergebniscaches mit der Abfrage zu senden.

[Grafana](/azure/data-explorer/grafana) enthält eine Konfigurationseinstellung für den Abfrageergebniscache auf Datenquellenebene, sodass alle Dashboards standardmäßig diese Einstellung verwenden und die Abfrage nicht geändert werden muss.

### <a name="configure-query-consistency"></a>Konfigurieren der Abfragekonsistenz

Für die Abfragekonsistenz stehen zwei Modelle zur Verfügung: *stark* (Standard) und *schwach*. Bei starker Konsistenz werden unabhängig davon, bei welchem Computeknoten die Abfrage eingeht, nur aktuelle, konsistente Daten angezeigt. Bei schwacher Konsistenz wird die Kopie der Metadaten regelmäßig durch die Knoten aktualisiert, was aufgrund der Synchronisierung von Metadatenänderungen zu einer Wartezeit von ein bis zwei Minuten führt. Mit dem schwachen Modell können Sie die Last auf dem Knoten verringern, von dem die Metadatenänderungen verwaltet werden, was eine höhere Parallelität als bei der standardmäßigen hohen Konsistenz ermöglicht. Legen Sie diese Konfiguration in [Clientanforderungseigenschaften](kusto/api/netfx/request-properties.md) und in den Grafana-Datenquellenkonfigurationen fest.

> [!NOTE]
> Falls Sie die Verzögerung für die Aktualisierung von Metadaten noch weiter verkürzen müssen, erstellen Sie ein Supportticket.

### <a name="optimize-queries"></a>Optimieren von Abfragen

Verwenden Sie die [bewährten Methoden für Abfragen](kusto/query/best-practices.md), um Ihre Abfragen so effizient wie möglich zu machen.

## <a name="set-cluster-policies"></a>Festlegen von Clusterrichtlinien

Die Anzahl gleichzeitiger Abfragen ist standardmäßig begrenzt und wird durch die [Richtlinie für die Anforderungsratenbegrenzung](kusto/management/request-rate-limit-policy.md) gesteuert, damit der Cluster nicht überlastet wird. Sie können diese Richtlinie für Situationen mit hoher Parallelität anpassen, die Anpassung sollte jedoch erst nach strengen Tests (vorzugsweise für produktionsähnliche Abfragemuster und Datasets) durchgeführt werden. Durch die Tests wird sichergestellt, dass der geänderte Wert für den Cluster geeignet ist. Dieser Grenzwert kann basierend auf den Anwendungsanforderungen konfiguriert werden.

## <a name="monitor-azure-data-explorer-clusters"></a>Überwachen von Azure Data Explorer-Clustern

Die Überwachung der Integrität Ihrer Clusterressourcen hilft bei der Ausarbeitung eines Optimierungsplans mit den in den obigen Abschnitten vorgeschlagenen Features. Azure Monitor für Azure Data Explorer bietet eine umfassende Übersicht über die Leistung, Vorgänge, Nutzung und Fehler Ihres Clusters. Klicken Sie im Azure-Portal im Überwachungsabschnitt von Azure Data Explorer auf die Registerkarte **Insights (Vorschau)** , um Einblicke in die Abfrageleistung sowie in gleichzeitige Abfragen, gedrosselte Abfragen und verschiedene andere Metriken zu erhalten. Details zur Überwachung von Clustern finden Sie in der Dokumentation [Azure Monitor für Azure Data Explorer (Vorschau)](/azure/azure-monitor/insights/data-explorer?toc=/azure/data-explorer/toc.json&amp;bc=/azure/data-explorer/breadcrumb/toc.json). Informationen zu den einzelnen Metriken finden Sie unter [Unterstützte Azure Data Explorer-Metriken](using-metrics.md#supported-azure-data-explorer-metrics).
