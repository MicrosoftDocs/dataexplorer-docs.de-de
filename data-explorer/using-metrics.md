---
title: Überwachen von Leistung, Integrität und Auslastung von Azure Data Explorer mit Metriken
description: Es wird beschrieben, wie Sie Azure Data Explorer-Metriken zum Überwachen der Leistung, Integrität und Nutzung des Clusters verwenden.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/19/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 3feac9960970c0605bafd07b3806a6eb39f9d9a5
ms.sourcegitcommit: 40f86b7f085152c21b6a1ee877f3ab324b59b88b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101838421"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Überwachen der Azure Data Explorer-Leistung, -Integrität und -Nutzung mit Metriken

Azure Data Explorer-Metriken liefern wichtige Hinweise in Bezug auf die Integrität und Leistung der Azure Data Explorer-Clusterressourcen. Verwenden Sie die in diesem Artikel beschriebenen Metriken, um die Nutzung, Integrität und Leistung von Azure Data Explorer-Clustern in Ihrem jeweiligen Szenario als eigenständige Metriken zu überwachen. Sie können Metriken auch als Basis für den Betrieb von [Azure-Dashboards](/azure/azure-portal/azure-portal-dashboards) und [Azure-Warnungen](/azure/azure-monitor/platform/alerts-metric-overview) verwenden.

Weitere Informationen zum Azure-Metrik-Explorer finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Sollten Sie über keins verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen.
* [Ein Cluster und eine Datenbank.](create-cluster-database-portal.md)

## <a name="use-metrics-to-monitor-your-azure-data-explorer-resources"></a>Überwachen Ihrer Azure Data Explorer-Ressourcen mithilfe von Metriken

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Suchen Sie im linken Bereich Ihres Azure Data Explorer-Clusters nach *Metriken*.
1. Wählen Sie **Metriken** aus, um den Bereich „Metriken“ zu öffnen und mit der Analyse Ihres Clusters zu beginnen.
    :::image type="content" source="media/using-metrics/select-metrics.gif" alt-text="Suchen nach und Auswählen von „Metriken“ im Azure-Portal":::

## <a name="work-in-the-metrics-pane"></a>Verwenden des Bereichs „Metriken“

Wählen Sie im Bereich „Metriken“ bestimmte Metriken für die Nachverfolgung sowie die gewünschte Aggregierung Ihrer Daten aus, und erstellen Sie Metrikdiagramme für die Anzeige auf Ihrem Dashboard.

In der Auswahl für die **Ressource** und den **Metriknamespace** ist Ihr Azure Data Explorer-Cluster bereits ausgewählt. Die Zahlen in der folgenden Abbildung entsprechen der nummerierten Liste weiter unten. Sie führen durch die verschiedenen Optionen im Zusammenhang mit der Einrichtung und Anzeige Ihrer Metriken.

![Bereich „Metriken“](media/using-metrics/metrics-pane.png)

1. Wählen Sie zum Erstellen eines Metrikdiagramms den Namen der **Metrik** und die relevante **Aggregation** pro Metrik aus. Weitere Informationen zu verschiedenen Metriken finden Sie unter [unterstützte Azure Data Explorer-Metriken](#supported-azure-data-explorer-metrics).
1. Wählen Sie **Metrik hinzufügen** aus, um mehrere Metriken im gleichen Diagramm anzuzeigen.
1. Wählen Sie **+ Neues Diagramm** aus, um mehrere Diagramme in einer einzelnen Ansicht anzuzeigen.
1. Verwenden Sie die Zeitauswahl, um den Zeitbereich zu ändern (Standardeinstellung: Letzte 24 Stunden).
1. Verwenden Sie [**Filter hinzufügen** und **Teilung anwenden**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) für Metriken, die über Dimensionen verfügen.
1. Wählen Sie die Option **An Dashboard anheften**, um Ihre Diagrammkonfiguration den Dashboards hinzuzufügen, damit Sie die Anzeige erneut verwenden können.
1. Legen Sie die Option **Neue Warnungsregel** fest, um Ihre Metriken mit den festgelegten Kriterien zu visualisieren. Die neue Warnungsregel enthält Ihre Dimensionen für Zielressource, Metrik, Teilung und Filter aus dem Diagramm. Ändern Sie diese Einstellungen im [Bereich für die Erstellung von Warnungsregeln](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

## <a name="supported-azure-data-explorer-metrics"></a>Unterstützte Azure Data Explorer-Metriken

Die Azure Data Explorer-Metriken geben Aufschluss über die Gesamtleistung und die Verwendung Ihrer Ressourcen und liefern Informationen zu bestimmten Aktionen wie Erfassung oder Abfrage. Die Metriken in diesem Artikel wurden nach Verwendungstyp gruppiert. 

Die Metriktypen umfassen Folgendes: 
* [Clustermetriken](#cluster-metrics) 
* [Exportieren von Metriken](#export-metrics) 
* [Erfassungsmetriken](#ingestion-metrics) 
* [Streamingerfassungsmetriken](#streaming-ingest-metrics)
* [Abfragemetriken](#query-metrics) 
* [Metriken der materialisierten Sicht](#materialized-view-metrics)

Eine alphabetische Liste der Azure Monitor-Metriken für Azure Data Explorer finden Sie in den [unterstützten Metriken für Azure Data Explorer-Cluster](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).

## <a name="cluster-metrics"></a>Clustermetriken

Die Clustermetriken dienen zum Nachverfolgen der allgemeinen Integrität des Clusters. Hierzu zählen beispielsweise die Ressourcen- und Erfassungsverwendung sowie die Reaktionsfähigkeit.

|**Metrik** | **Einheit** | **Aggregation** | **Beschreibung der Metrik** | **Dimensionen** |
|---|---|---|---|---|
| Cacheauslastung | Percent | Avg, Max, Min | Prozentsatz der zugewiesenen Cacheressourcen, die derzeit vom Cluster genutzt werden. Cache ist die Größe der SSD, die für die Benutzeraktivität gemäß der definierten Cacherichtlinie zugeordnet wird. <br> <br> Eine durchschnittliche Cacheauslastung von 80 % oder weniger ist für einen Cluster ein tragbarer Zustand. Wenn die durchschnittliche Cacheauslastung 80 Prozent übersteigt, sollte der Cluster <br> auf einen datenspeicheroptimierten Tarif [hochskaliert](manage-cluster-vertical-scaling.md) oder <br> auf mehr Instanzen [aufskaliert](manage-cluster-horizontal-scaling.md) werden. Alternativ hierzu können Sie die Cacherichtlinie anpassen (weniger Tage im Cache). Falls die Cacheauslastung über 100 % liegt, übersteigt die Größe der zwischenzuspeichernden Daten gemäß Cacherichtlinie die Gesamtgröße des Caches im Cluster. | Keine |
| CPU | Percent | Avg, Max, Min | Prozentsatz der zugewiesenen Computeressourcen, die derzeit von Computern im Cluster genutzt werden. <br> <br> Eine durchschnittliche CPU-Auslastung von 80 % ist für einen Cluster ein tragbarer Zustand. Der Maximalwert der CPU beträgt 100 %. Dies bedeutet, dass es keine weiteren Computeressourcen zum Verarbeiten von Daten gibt. <br> Wenn die Leistung eines Clusters nicht gut ist, sollten Sie den Maximalwert der CPU überprüfen, um zu ermitteln, ob bestimmte CPUs blockiert sind. | Keine |
| Datenerfassungsauslastung | Percent | Avg, Max, Min | Prozentsatz der tatsächlichen Ressourcen zum Erfassen von Daten von den gesamten Ressourcen, die in der Kapazitätsrichtlinie für die Erfassung zugeordnet sind. Die Standardrichtlinie für die Kapazität umfasst nicht mehr als 512 gleichzeitige Erfassungsvorgänge oder 75 % der Clusterressourcen, die für die Erfassung genutzt werden. <br> <br> Eine durchschnittliche Erfassungsauslastung von 80 % oder weniger ist für einen Cluster ein tragbarer Zustand. Der Maximalwert der Erfassungsauslastung beträgt 100 %. Dies bedeutet, dass die gesamte Erfassungskapazität des Clusters genutzt wird und unter Umständen eine Erfassungswarteschlange entsteht. | Keine |
| Keep-Alive | Anzahl | Avg | Dient zum Nachverfolgen der Reaktionsfähigkeit des Clusters. <br> <br> Für einen Cluster mit höchster Reaktionsfähigkeit wird der Wert 1 zurückgegeben, und für einen blockierten oder getrennten Cluster lautet der Wert 0. |
| Gesamtanzahl gedrosselter Befehle | Anzahl | Avg, Max, Min, Sum | Die Anzahl der gedrosselten (abgelehnten) Befehle im Cluster, da die maximal zulässige Anzahl gleichzeitiger (paralleler) Befehle erreicht wurde. | Keine |
| Gesamtanzahl von Erweiterungen | Anzahl | Avg, Max, Min, Sum | Gesamtanzahl der Datenerweiterungen im Cluster. <br> <br> Änderungen an dieser Metrik können massive Datenstrukturänderungen und eine hohe Auslastung des Clusters mit sich bringen, da das Zusammenführen von Datenerweiterungen eine CPU-intensive Aktivität ist. | Keine |

## <a name="export-metrics"></a>Exportieren von Metriken

Exportmetriken dienen zum Nachverfolgen der allgemeinen Integrität und Leistung von Exportvorgängen anhand von Aspekten wie Verzögerung, Ergebnissen, Anzahl von Datensätzen und Auslastung.

|**Metrik** | **Einheit** | **Aggregation** | **Beschreibung der Metrik** | **Dimensionen** |
|---|---|---|---|---|
Fortlaufender Export – Anzahl der exportierten Datensätze    | Anzahl | SUM | Die Anzahl exportierter Datensätze in allen Aufträgen mit fortlaufendem Export. | ContinuousExportName |
Fortlaufender Export – maximale Verzögerung |    Anzahl   | Max   | Die Verzögerung (in Minuten), die von Aufträgen mit fortlaufendem Export im Cluster gemeldet wurde. | Keine |
Fortlaufender Export – ausstehende Anzahl | Anzahl | Max   | Die Anzahl ausstehender Aufträge mit fortlaufendem Export. Diese Aufträge sind ausführungsbereit, befinden sich aber in einer Warteschlange (möglicherweise aufgrund von unzureichender Kapazität). 
Fortlaufender Export – Ergebnis    | Anzahl |   Anzahl   | Das Ergebnis (Fehler/Erfolg) der einzelnen Ausführungen des fortlaufenden Exports. | ContinuousExportName |
Exportauslastung |    Percent | Max   | Die genutzte Exportkapazität relativ zur Gesamtkapazität im Cluster (zwischen 0 und 100). | Keine |

## <a name="ingestion-metrics"></a>Erfassungsmetriken

Erfassungsmetriken dienen zum Nachverfolgen der allgemeinen Integrität und Leistung von Erfassungsvorgängen anhand von Aspekten wie Wartezeit, Ergebnissen und Volumen.

> [!NOTE]
> * [Wenden Sie Filter auf Diagramme an](/azure/azure-monitor/platform/metrics-charts#apply-filters-to-charts), um partielle Daten nach Dimensionen darzustellen. Untersuchen Sie beispielsweise die Erfassung in einem bestimmten `Database`-Element.
> * [Wenden Sie die Teilung auf ein Diagramm an](/azure/azure-monitor/platform/metrics-charts#apply-splitting-to-a-chart), um Daten nach verschiedenen Komponenten zu visualisieren. Dieser Vorgang ist nützlich für die Analyse von Metriken, die in den einzelnen Schritten der Erfassungspipeline gemeldet werden, z. B. `Blobs received`.

|**Metrik** | **Einheit** | **Aggregation** | **Beschreibung der Metrik** | **Dimensionen** |
|---|---|---|---|---|
| Batchblob – Anzahl  | Anzahl | Avg, Max, Min | Anzahl der Datenquellen in einem abgeschlossenen Batch für die Erfassung | Datenbank |
| Batchdauer    | Sekunden | Avg, Max, Min | Dauer der Batchverarbeitungsphase im Erfassungsflow  | Datenbank |
| Batchgröße        | Byte | Avg, Max, Min | Nicht komprimierte erwartete Datengröße in einem aggregierten Batch für die Erfassung | Datenbank |
| Verarbeitete Batches | Anzahl | Sum, Max, Min | Anzahl der für die Erfassung abgeschlossenen Batches <br> `Batching Type`: Gibt an, ob die Fertigstellung des Batches auf der Batchverarbeitungszeit oder dem Grenzwert für Datengröße/Dateianzahl in der [Batchrichtlinie](./kusto/management/batchingpolicy.md) basierte. | Datenbank, Batchverarbeitungstyp |
| Empfangene Blobs    | Anzahl | Sum, Max, Min | Die Anzahl der von einer Komponente aus dem Eingabestream empfangenen Blobs. <br> <br> Verwenden Sie **Teilung anwenden**, um die einzelnen Komponenten zu analysieren. | Datenbank, Komponententyp, Komponentenname |
| Verarbeitete Blobs   | Anzahl | Sum, Max, Min | Die Anzahl der von einer Komponente verarbeiteten Blobs. <br> <br> Verwenden Sie **Teilung anwenden**, um die einzelnen Komponenten zu analysieren. | Datenbank, Komponententyp, Komponentenname |
| Gelöschte Blobs     | Anzahl | Sum, Max, Min | Die Anzahl der von einer Komponente dauerhaft gelöschten Blobs. Für jeden dieser Blobs wird eine Metrik vom Typ `Ingestion result` mit der Fehlerursache gesendet. <br> <br> Verwenden Sie **Teilung anwenden**, um die einzelnen Komponenten zu analysieren. | Datenbank, Komponententyp, Komponentenname |
| Wartezeit bei der Ermittlung | Sekunden | Avg | Die Zeit zwischen dem Einreihen von Daten in die Warteschlange und der Erkennung durch die Datenverbindungen. Diese Zeit ist nicht in der Metrik **Phasenlatenz** oder **Erfassungslatenz** enthalten. | Komponententyp, Komponentenname |
| Empfangene Ereignisse   | Anzahl | Sum, Max, Min | Anzahl der von den Datenverbindungen aus dem Eingabestream empfangenen Ereignisse | Komponententyp, Komponentenname |
| Verarbeitete Ereignisse  | Anzahl | Sum, Max, Min | Anzahl der von den Datenverbindungen verarbeiteten Ereignisse | Komponententyp, Komponentenname | 
| Gelöschte Ereignisse    | Anzahl | Sum, Max, Min | Anzahl der von den Datenverbindungen dauerhaft gelöschten Ereignisse | Komponententyp, Komponentenname | 
| Verarbeitete Ereignisse (für Event/IoT Hub) | Anzahl | Max, Min, Sum | Gesamtzahl der Ereignisse, die von Event Hubs gelesen und vom Cluster verarbeitet werden. Diese Ereignisse werden in zwei Gruppen unterteilt: vom Clustermodul abgelehnte Ereignisse und vom Cluster akzeptierte Ereignisse. | Status |
| Latenz bei der Erfassung | Sekunden | Avg, Max, Min | Latenz der erfassten Daten ab dem Empfangszeitpunkt der Daten im Cluster bis zu dem Zeitpunkt, zu dem die Daten bereit zum Abfragen sind. Der Zeitraum der Erfassungslatenz richtet sich nach dem Erfassungsszenario. | Keine |
| Ergebnis der Datenerfassung  | Anzahl | SUM | Gesamtzahl von nicht erfolgreichen oder erfolgreichen Erfassungsvorgängen. <br> <br> Verwenden Sie die Option **Teilung anwenden**, um Buckets mit Erfolgs- und Fehlerergebnissen zu erstellen und die Dimensionen zu analysieren (**Wert** > **Status**). <br>Weitere Informationen zu möglichen Fehlerergebnissen finden Sie unter [Erfassungsfehlercodes in Azure Data Explorer](error-codes.md).| Status |
| Datenerfassungsvolumen (in MB) | Anzahl | Max, Sum | Die Gesamtgröße der im Cluster erfassten Daten (in MB) vor der Komprimierung. | Datenbank |
| Warteschlangenlänge | Anzahl | Avg | Anzahl ausstehender Nachrichten in der Eingabewarteschlange einer Komponente | Komponententyp |
| Älteste Nachricht in Warteschlange | Sekunden | Avg | Zeit in Sekunden ab dem Zeitpunkt, zu dem die älteste Nachricht in die Eingabewarteschlange einer Komponente eingefügt wurde | Komponententyp | 
| Größe der empfangenen Daten in Bytes | Byte | Avg, Sum | Größe der von den Datenverbindungen aus dem Eingabestream empfangenen Daten | Komponententyp, Komponentenname |
| Phasenlatenz | Sekunden | Avg | Zeit ab dem Zeitpunkt, zu dem eine Nachricht von Azure Data Explorer erkannt wird, bis zu dem Zeitpunkt, zu dem ihr Inhalt von einer Erfassungskomponente zur Verarbeitung empfangen wird. <br> <br> Verwenden Sie die Option **Filter anwenden**, und wählen Sie **Komponententyp > EngineStorage** aus, um die gesamte Erfassungslatenz anzuzeigen.| Datenbank, Komponententyp | 

## <a name="streaming-ingest-metrics"></a>Streamingerfassungsmetriken

Streamingerfassungsmetriken verfolgen Streamingerfassungsdaten sowie Anforderungsrate, Dauer und Ergebnisse nach.

|**Metrik** | **Einheit** | **Aggregation** | **Beschreibung der Metrik** | **Dimensionen** |
|---|---|---|---|---|
Datenrate der Streamingerfassung |    Anzahl   | RateRequestsPerSecond | Gesamtmenge der im Cluster erfassten Daten. | Keine |
Dauer der Streamingerfassung   | Millisekunden  | Avg, Max, Min | Gesamtdauer aller Streamingerfassungsanforderungen. | Keine |
Anforderungsrate der Streamingerfassung   | Anzahl | Count, Avg, Max, Min, Sum | Gesamtanzahl der Streamingerfassungsanforderungen. | Keine |
Ergebnis der Streamingerfassung | Anzahl | Avg   | Gesamtanzahl der Streamingerfassungsanforderungen nach Ergebnistyp. | Ergebnis |

## <a name="query-metrics"></a>Abfragemetriken

Abfrageleistungsmetriken verfolgen die Abfragedauer und Gesamtanzahl gleichzeitiger oder gedrosselter Abfragen nach.

|**Metrik** | **Einheit** | **Aggregation** | **Beschreibung der Metrik** | **Dimensionen** |
|---|---|---|---|---|
| Abfragedauer | Millisekunden | Avg, Min, Max, Sum | Gesamtzeit bis zum Empfangen der Abfrageergebnisse (ohne Netzwerklatenz). | QueryStatus |
| Gesamtanzahl gleichzeitiger Abfragen | Anzahl | Avg, Max, Min, Sum | Die Anzahl der Abfragen, die im Cluster parallel ausgeführt werden. Diese Metrik ist eine gute Möglichkeit, um die Auslastung des Clusters einzuschätzen. | Keine |
| Gesamtanzahl gedrosselter Abfragen | Anzahl | Avg, Max, Min, Sum | Die Anzahl der gedrosselten (abgelehnten) Abfragen im Cluster. Die maximal zulässige Anzahl gleichzeitiger (paralleler) Abfragen wird in der Richtlinie für die Anforderungsratenbegrenzung definiert. | Keine |

## <a name="materialized-view-metrics"></a>Metriken der materialisierten Sicht

|**Metrik** | **Einheit** | **Aggregation** | **Beschreibung der Metrik** | **Dimensionen** |
|---|---|---|---|---|
|MaterializedViewHealth                    | 1, 0    | Avg     |  Der Wert ist „1“, wenn die Sicht als fehlerfrei angesehen wird, und andernfalls „0“. | Database, MaterializedViewName |
|MaterializedViewAgeMinutes                | Minuten | Avg     | Das Alter (`age`) der Sicht ist definiert durch den aktuellen Zeitpunkt abzüglich des letzten Erfassungszeitpunkts, der von der Sicht verarbeitet wurde. Der Metrikwert ist die Zeit in Minuten (je niedriger der Wert, desto „fehlerfreier“ ist die Sicht). | Database, MaterializedViewName |
|MaterializedViewResult                    | 1       | Avg     | Die Metrik enthält eine Dimension vom Typ `Result`, die das Ergebnis des letzten Materialisierungszyklus angibt. (Weitere Informationen zu möglichen Werten finden Sie unter [MaterializedViewResult-Metrik](kusto/management/materialized-views/materialized-view-overview.md#materializedviewresult-metric).) Der Metrikwert ist immer „1“. | Database, MaterializedViewName, Result |
|MaterializedViewRecordsInDelta            | Anzahl von Datensätzen | Avg | Die Anzahl von Datensätzen, die sich derzeit im nicht verarbeiteten Teil der Quelltabelle befinden. Weitere Informationen finden Sie im Abschnitt zur [Funktionsweise von materialisierten Sichten](./kusto/management/materialized-views/materialized-view-overview.md#how-materialized-views-work).| Database, MaterializedViewName |
|MaterializedViewExtentsRebuild            | Anzahl von Erweiterungen | Avg | Die Anzahl von Erweiterungen („Extents“), die während des Materialisierungszyklus neu erstellt wurden. | Database, MaterializedViewName|
|MaterializedViewDataLoss                  | 1       | Max    | Die Metrik wird ausgelöst, wenn für nicht verarbeitete Quelldaten demnächst der Aufbewahrungszeitraum beginnt. Gibt an, dass die materialisierte Sicht fehlerhaft ist. | Database, MaterializedViewName, Kind |

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Erfassen und Abfragen von Überwachungsdaten in Azure Data Explorer](ingest-data-no-code.md)
* [Überwachen von Azure Data Explorer-Erfassungsvorgängen mithilfe von Diagnoseprotokollen](using-diagnostic-logs.md)
* [Schnellstart: Abfragen von Daten im Azure-Daten-Explorer](web-query-data.md)
