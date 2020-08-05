---
title: Überwachen von Leistung, Integrität und Auslastung von Azure Data Explorer mit Metriken
description: Es wird beschrieben, wie Sie Azure Data Explorer-Metriken zum Überwachen der Leistung, Integrität und Nutzung des Clusters verwenden.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 2b90f029d881c1f050bce4d458f08e9f1ba20ef9
ms.sourcegitcommit: afc369ab4c4bcc74f2dce22b397a340572db8ecf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87528162"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Überwachen der Azure Data Explorer-Leistung, -Integrität und -Nutzung mit Metriken

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. Um den Azure Data Explorer zu verwenden, erstellen Sie zuerst einen Cluster und anschließend eine oder mehrere Datenbanken in diesem Cluster. Anschließend erfassen (laden) Sie Daten in eine Datenbank, damit Sie diese abfragen können. Azure Data Explorer-Metriken liefern wichtige Hinweise in Bezug auf die Integrität und Leistung der Clusterressourcen. Verwenden Sie die in diesem Artikel beschriebenen Metriken, um die Integrität und Leistung des Azure Data Explorer-Clusters in Ihrem jeweiligen Szenario als eigenständige Metriken zu überwachen. Sie können Metriken auch als Basis für den Betrieb von [Azure-Dashboards](/azure/azure-portal/azure-portal-dashboards) und [Azure-Warnungen](/azure/azure-monitor/platform/alerts-metric-overview) verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Sollten Sie über keins verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen.
* [Ein Cluster und eine Datenbank.](create-cluster-database-portal.md)

## <a name="using-metrics"></a>Verwenden von Metriken

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie in Ihrem Azure Data Explorer-Cluster die Option **Metriken**, um den Bereich „Metriken“ zu öffnen und mit der Analyse Ihres Clusters zu beginnen.
    ![Auswählen von „Metriken“](media/using-metrics/select-metrics.png)
1. Im Bereich „Metriken“:  ![Bereich „Metriken“](media/using-metrics/metrics-pane.png)
    1. Wählen Sie zum Erstellen eines Metrikdiagramms den Namen der **Metrik** und die relevante **Aggregation** pro Metrik aus. In der Auswahl für die **Ressource** und den **Metriknamespace** ist Ihr Azure Data Explorer-Cluster bereits ausgewählt. Weitere Informationen zu verschiedenen Metriken finden Sie unter [unterstützte Azure Data Explorer-Metriken](#supported-azure-data-explorer-metrics).
    1. Wählen Sie **Metrik hinzufügen** aus, um mehrere Metriken im gleichen Diagramm anzuzeigen.
    1. Wählen Sie **+ Neues Diagramm** aus, um mehrere Diagramme in einer einzelnen Ansicht anzuzeigen.
    1. Verwenden Sie die Zeitauswahl, um den Zeitbereich zu ändern (Standardeinstellung: Letzte 24 Stunden).
    1. Verwenden Sie [**Filter hinzufügen** und **Teilung anwenden**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) für Metriken, die über Dimensionen verfügen.
    1. Wählen Sie die Option **An Dashboard anheften**, um Ihre Diagrammkonfiguration den Dashboards hinzuzufügen, damit Sie die Anzeige erneut verwenden können.
    1. Legen Sie die Option **Neue Warnungsregel** fest, um Ihre Metriken mit den festgelegten Kriterien zu visualisieren. Die neue Warnungsregel enthält Ihre Dimensionen für Zielressource, Metrik, Teilung und Filter aus dem Diagramm. Ändern Sie diese Einstellungen im [Bereich für die Erstellung von Warnungsregeln](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Weitere Informationen zur Verwendung von [Metrik-Explorer](/azure/azure-monitor/platform/metrics-getting-started)

## <a name="supported-azure-data-explorer-metrics"></a>Unterstützte Azure Data Explorer-Metriken

Die unterstützten Azure Data Explorer-Metriken sind entsprechend ihrer Nutzung in verschiedene Kategorien unterteilt. 

### <a name="cluster-health-metrics"></a>Clusterintegritätsmetriken

Die Clusterintegritätsmetriken verfolgen die allgemeine Integrität des Clusters nach. Dazu gehören die Ressourcen- und Datenerfassungsauslastung sowie die Reaktionsfähigkeit.

**Metrik** | **Einheit** | **Aggregation** | **Beschreibung der Metrik** | **Dimensionen** |
|---|---|---|---|---|
| Cacheauslastung | Percent | Avg, Max, Min | Prozentsatz der zugewiesenen Cacheressourcen, die derzeit vom Cluster genutzt werden. Cache ist die Größe der SSD, die für die Benutzeraktivität gemäß der definierten Cacherichtlinie zugeordnet wird. Eine durchschnittliche Cacheauslastung von 80 % oder weniger ist für einen Cluster ein tragbarer Zustand. Wenn die durchschnittliche Cacheauslastung über 80 % liegt, sollte für den Cluster das [zentrale Hochskalieren](manage-cluster-vertical-scaling.md) auf einen Tarif mit Datenspeicheroptimierung oder das [horizontale Hochskalieren](manage-cluster-horizontal-scaling.md) auf weitere Instanzen durchgeführt werden. Alternativ hierzu können Sie die Cacherichtlinie anpassen (weniger Tage im Cache). Falls die Cacheauslastung über 100 % liegt, übersteigt die Größe der zwischenzuspeichernden Daten gemäß Cacherichtlinie die Gesamtgröße des Caches im Cluster. | Keine |
| CPU | Percent | Avg, Max, Min | Prozentsatz der zugewiesenen Computeressourcen, die derzeit von Computern im Cluster genutzt werden. Eine durchschnittliche CPU-Auslastung von 80 % ist für einen Cluster ein tragbarer Zustand. Der Maximalwert der CPU beträgt 100 %. Dies bedeutet, dass es keine weiteren Computeressourcen zum Verarbeiten von Daten gibt. Wenn die Leistung eines Clusters nicht gut ist, sollten Sie den Maximalwert der CPU überprüfen, um zu ermitteln, ob bestimmte CPUs blockiert sind. | Keine |
| Datenerfassungsauslastung | Percent | Avg, Max, Min | Prozentsatz der tatsächlichen Ressourcen zum Erfassen von Daten von den gesamten Ressourcen, die in der Kapazitätsrichtlinie für die Erfassung zugeordnet sind. Die Standardrichtlinie für die Kapazität umfasst nicht mehr als 512 gleichzeitige Erfassungsvorgänge oder 75 % der Clusterressourcen, die für die Erfassung genutzt werden. Eine durchschnittliche Erfassungsauslastung von 80 % oder weniger ist für einen Cluster ein tragbarer Zustand. Der Maximalwert der Erfassungsauslastung beträgt 100 %. Dies bedeutet, dass die gesamte Erfassungskapazität des Clusters genutzt wird und unter Umständen eine Erfassungswarteschlange entsteht. | Keine |
| Keep-Alive | Anzahl | Avg | Dient zum Nachverfolgen der Reaktionsfähigkeit des Clusters. Für einen Cluster mit höchster Reaktionsfähigkeit wird der Wert 1 zurückgegeben, und für einen blockierten oder getrennten Cluster lautet der Wert 0. |
| Gesamtanzahl gedrosselter Befehle | Anzahl | Avg, Max, Min, Sum | Die Anzahl der gedrosselten (abgelehnten) Befehle im Cluster, da die maximal zulässige Anzahl gleichzeitiger (paralleler) Befehle erreicht wurde. | Keine |
| Gesamtanzahl von Erweiterungen | Anzahl | Avg, Max, Min, Sum | Gesamtanzahl der Datenerweiterungen im Cluster. Änderungen an dieser Metrik können massive Datenstrukturänderungen und eine hohe Auslastung des Clusters mit sich bringen, da das Zusammenführen von Datenerweiterungen eine CPU-intensive Aktivität ist. | Keine |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Exportieren von Integritäts- und Leistungsmetriken

Durch das Exportieren von Integritäts- und Leistungsmetriken wird die allgemeine Integrität und Leistung von Exportvorgängen wie Verzögerung, Ergebnisse, Anzahl von Datensätzen und Auslastung nachverfolgt.

**Metrik** | **Einheit** | **Aggregation** | **Beschreibung der Metrik** | **Dimensionen** |
|---|---|---|---|---|
Fortlaufender Export – Anzahl der exportierten Datensätze    | Anzahl | SUM | Die Anzahl exportierter Datensätze in allen Aufträgen mit fortlaufendem Export. | Keine |
Fortlaufender Export – maximale Verzögerung |    Anzahl   | Max   | Die Verzögerung (in Minuten), die von Aufträgen mit fortlaufendem Export im Cluster gemeldet wurde. | Keine |
Fortlaufender Export – ausstehende Anzahl | Anzahl | Max   | Die Anzahl ausstehender Aufträge mit fortlaufendem Export. Diese Aufträge sind ausführungsbereit, befinden sich aber in einer Warteschlange (möglicherweise aufgrund von unzureichender Kapazität). 
Fortlaufender Export – Ergebnis    | Anzahl |   Anzahl   | Das Ergebnis (Fehler/Erfolg) der einzelnen Ausführungen des fortlaufenden Exports. | ContinuousExportName |
Exportauslastung |    Percent | Max   | Die genutzte Exportkapazität relativ zur Gesamtkapazität im Cluster (zwischen 0 und 100). | Keine |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Erfassung von Integritäts- und Leistungsmetriken

Die Erfassung von Integritäts- und Leistungsmetriken verfolgt die allgemeine Integrität und Leistung von Erfassungsvorgängen wie Latenz, Ergebnisse und Volumen nach.

**Metrik** | **Einheit** | **Aggregation** | **Beschreibung der Metrik** | **Dimensionen** |
|---|---|---|---|---|
| Batchblob – Anzahl | Anzahl | Avg, Max, Min | Anzahl der Datenquellen in einem abgeschlossenen Batch für die Erfassung | Datenbank |
| Batchdauer | Sekunden | Avg, Max, Min | Dauer der Batchverarbeitungsphase im Erfassungsflow  | Datenbank |
| Batchgröße | Byte | Avg, Max, Min | Nicht komprimierte erwartete Datengröße in einem aggregierten Batch für die Erfassung | Datenbank |
| Verarbeitete Batches | Anzahl | Avg, Max, Min | Anzahl der für die Erfassung abgeschlossenen Batches `BatchCompletionReason`: Gibt an, ob die Batchverarbeitungszeit oder das Limit für Datengröße/Dateianzahl in der [Batchrichtlinie](/azure/data-explorer/kusto/management/batchingpolicy) erreicht wurde | Datenbank, BatchCompletionReason |
| Verarbeitete Ereignisse (für Event/IoT Hub) | Anzahl | Max, Min, Sum | Gesamtzahl der Ereignisse, die von Event Hubs gelesen und vom Cluster verarbeitet werden. Die Ereignisse werden danach unterteilt, ob sie vom Clustermodul abgelehnt oder akzeptiert werden. | EventStatus |
| Latenz bei der Erfassung | Sekunden | Avg, Max, Min | Latenz der erfassten Daten ab dem Empfangszeitpunkt der Daten im Cluster bis zu dem Zeitpunkt, zu dem die Daten bereit zum Abfragen sind. Der Zeitraum der Erfassungslatenz richtet sich nach dem Erfassungsszenario. | Keine |
| Ergebnis der Datenerfassung | Anzahl | Anzahl | Gesamtzahl von nicht erfolgreichen und erfolgreichen Erfassungsvorgängen. Verwenden Sie die Option **Teilung anwenden**, um Buckets mit Erfolgs- und Fehlerergebnissen zu erstellen und die Dimensionen zu analysieren (**Wert** > **Status**).| IngestionResultDetails |
| Datenerfassungsvolumen (in MB) | Anzahl | Max, Sum | Die Gesamtgröße der im Cluster erfassten Daten (in MB) vor der Komprimierung. | Datenbank |
| | | | |  

### <a name="query-performance"></a>Abfrageleistung

Abfrageleistungsmetriken verfolgen die Abfragedauer und Gesamtanzahl gleichzeitiger oder gedrosselter Abfragen nach.

**Metrik** | **Einheit** | **Aggregation** | **Beschreibung der Metrik** | **Dimensionen** |
|---|---|---|---|---|
| Abfragedauer | Millisekunden | Avg, Min, Max, Sum | Gesamtzeit bis zum Empfangen der Abfrageergebnisse (ohne Netzwerklatenz). | QueryStatus |
| Gesamtanzahl gleichzeitiger Abfragen | Anzahl | Avg, Max, Min, Sum | Die Anzahl der Abfragen, die im Cluster parallel ausgeführt werden. Diese Metrik ist eine gute Möglichkeit, um die Auslastung des Clusters einzuschätzen. | Keine |
| Gesamtanzahl gedrosselter Abfragen | Anzahl | Avg, Max, Min, Sum | Die Anzahl der gedrosselten (abgelehnten) Abfragen im Cluster. Die maximal zulässige Anzahl gleichzeitiger (paralleler) Abfragen wird in der Richtlinie für gleichzeitige Abfragen definiert. | Keine |
| | | | |

### <a name="streaming-ingest-metrics"></a>Streamingerfassungsmetriken

Streamingerfassungsmetriken verfolgen Streamingerfassungsdaten sowie Anforderungsrate, Dauer und Ergebnisse nach.

**Metrik** | **Einheit** | **Aggregation** | **Beschreibung der Metrik** | **Dimensionen** |
|---|---|---|---|---|
Datenrate der Streamingerfassung |    Anzahl   | RateRequestsPerSecond | Gesamtmenge der im Cluster erfassten Daten. | Keine |
Dauer der Streamingerfassung   | Millisekunden  | Avg, Max, Min | Gesamtdauer aller Streamingerfassungsanforderungen. | Keine |
Anforderungsrate der Streamingerfassung   | Anzahl | Count, Avg, Max, Min, Sum | Gesamtanzahl der Streamingerfassungsanforderungen. | Keine |
Ergebnis der Streamingerfassung | Anzahl | Avg   | Gesamtanzahl der Streamingerfassungsanforderungen nach Ergebnistyp. | Ergebnis |
| | | | |

Weitere Informationen zu unterstützten Azure Data Explorer-Clustermetriken finden Sie [hier](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).


## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Erfassen und Abfragen von Überwachungsdaten in Azure Data Explorer](ingest-data-no-code.md)
* [Überwachen von Azure Data Explorer-Erfassungsvorgängen mithilfe von Diagnoseprotokollen](using-diagnostic-logs.md)
* [Schnellstart: Abfragen von Daten im Azure-Daten-Explorer](web-query-data.md)
