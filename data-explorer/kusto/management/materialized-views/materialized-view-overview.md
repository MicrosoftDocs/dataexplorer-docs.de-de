---
title: 'Materialisierte Sichten: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt materialisierte Sichten in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 407db347d4d21450d5648fe8716e2d82553a9669
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320654"
---
# <a name="materialized-views-preview"></a>Materialisierte Sichten (Vorschau)

[Materialisierte Sichten](../../query/materialized-view-function.md) machen eine *Aggregations* Abfrage über eine Quell Tabelle verfügbar. Materialisierte Sichten geben immer ein Aktuelles Ergebnis der Aggregations Abfrage zurück (immer aktuell). Das [Abfragen einer materialisierten Sicht](#materialized-views-queries) ist leistungsfähiger als die direkte Ausführung der Aggregation über die Quell Tabelle, die jede Abfrage durchführt.

> [!NOTE]
> Materialisierte Sichten weisen einige [Einschränkungen](materialized-view-create.md#limitations-on-creating-materialized-views)auf und funktionieren nicht garantiert für alle Szenarien. Überprüfen Sie die [Leistungs Überlegungen](#performance-considerations) , bevor Sie mit dem Feature arbeiten.

Verwenden Sie die folgenden Befehle, um materialisierte Sichten zu verwalten:
* [`.create materialized-view`](materialized-view-create.md)
* [`.alter materialized-view`](materialized-view-alter.md)
* [`.drop materialized-view`](materialized-view-drop.md)
* [`.disable | .enable materialized-view`](materialized-view-enable-disable.md)
* [`.show materialized-views commands`](materialized-view-show-commands.md)

## <a name="why-use-materialized-views"></a>Gründe für die Verwendung materialisierter Sichten

Durch die Investition von Ressourcen (Datenspeicherung, CPU-Hintergrund Zyklen) für materialisierte Sichten häufig verwendeter Aggregationen haben Sie die folgenden Vorteile:

* **Leistungsverbesserung:** Das Abfragen einer materialisierten Sicht führt häufig zu einer besseren Leistung als das Abfragen der Quell Tabelle für dieselbe Aggregations Funktion (en).

* Aktualität **:** Eine materialisierte Sicht Abfrage gibt immer die aktuellen Ergebnisse zurück, unabhängig davon, wann die Materialisierung zuletzt stattfindet. Die Abfrage kombiniert den materialisierten Teil der Sicht mit den Datensätzen in der Quell Tabelle, die noch nicht materialisiert wurden (der `delta` Teil), wobei immer die aktuellsten Ergebnisse bereitgestellt werden.

* **Kostenreduzierung:** beim [Abfragen einer materialisierten Sicht](#materialized-views-queries) werden weniger Ressourcen aus dem Cluster beansprucht als durch die Aggregation der Quell Tabelle. Die Beibehaltungs Richtlinie der Quell Tabelle kann reduziert werden, wenn nur eine Aggregation erforderlich ist. Durch diese Einrichtung werden die Kosten für den Hot Cache für die Quell Tabelle reduziert.

## <a name="materialized-views-use-cases"></a>Anwendungsfälle für materialisierte Sichten

Im folgenden werden gängige Szenarien beschrieben, die mithilfe einer materialisierten Sicht adressiert werden können:

* Abfragen des letzten Datensatzes pro Entität mithilfe von [ `arg_max()` (Aggregations Funktion)](../../query/arg-max-aggfunction.md).
* Deduplizieren von Datensätzen in einer Tabelle mithilfe von [ `any()` (Aggregations Funktion)](../../query/any-aggfunction.md).
* Reduzieren Sie die Datenauflösung, indem Sie regelmäßige Statistiken über die Rohdaten berechnen. Verwenden Sie verschiedene [Aggregations Funktionen](materialized-view-create.md#supported-aggregation-functions) nach Zeit.
    * Verwenden `T | summarize dcount(User) by bin(Timestamp, 1d)` Sie z. b., um eine aktuelle Momentaufnahme von unterschiedlichen Benutzern pro Tag beizubehalten.

Beispiele für alle Anwendungsfälle finden Sie unter [materialisierte View Create Command](materialized-view-create.md#examples).

## <a name="how-materialized-views-work"></a>Funktionsweise materialisierter Sichten

Eine materialisierte Sicht besteht aus zwei Komponenten:

* Ein *materialisierter* Teil: eine Azure Daten-Explorer Tabelle, die aggregierte Datensätze aus der Quell Tabelle enthält, die bereits verarbeitet wurden.  Diese Tabelle enthält immer einen einzelnen Datensatz gemäß der Group-by-Kombination der Aggregation.
* Ein *Delta* : die neu erfassten Datensätze in der Quell Tabelle, die noch nicht verarbeitet wurden.

Beim Abfragen der materialisierten Sicht wird der materialisierte Teil mit dem Delta Teil kombiniert, und es wird ein Aktuelles Ergebnis der Aggregations Abfrage bereitgestellt. Der Offline-Materialisierungs Prozess erfasst neue Datensätze aus der *Delta* -in die materialisierte Tabelle und ersetzt vorhandene Datensätze. Der Austausch erfolgt durch das Neuerstellen von Blöcken, die zu ersetzende Datensätze enthalten. Wenn sich die Datensätze im *Delta* ständig mit allen Daten-Shards im *materialisierten* Teil schneiden, erfordert jeder Materialisierungs Vorgang das Neuerstellen des gesamten *materialisierten* Teils und wird möglicherweise nicht mit der Erfassungs Rate aufrechterhalten. In diesem Fall wird die Ansicht fehlerhaft, und das *Delta* wird ständig vergrößert.
Im Abschnitt " [Überwachung](#materialized-views-monitoring) " wird erläutert, wie Sie Probleme beheben können.

## <a name="materialized-views-queries"></a>Abfragen für materialisierte Sichten

Die primäre Methode, eine materialisierte Sicht abzufragen, ist der Name, wie z. b. das Abfragen eines Tabellen Verweises. Wenn die materialisierte Sicht abgefragt wird, wird der materialisierte Teil der Ansicht mit den Datensätzen in der Quell Tabelle kombiniert, die noch nicht materialisiert wurden. Wenn Sie die materialisierte Sicht Abfragen, werden immer die aktuellsten Ergebnisse zurückgegeben, basierend auf allen Datensätzen, die in der Quell Tabelle erfasst wurden. Weitere Informationen zur Aufschlüsselung der materialisierten Ansichts Teile finden Sie unter [Funktionsweise materialisierter Sichten](#how-materialized-views-work). 

Eine weitere Möglichkeit zum Abfragen der Sicht besteht darin, die- [ `materialized_view()` Funktion](../../query/materialized-view-function.md)zu verwenden. Diese Option unterstützt das Abfragen nur des materialisierten Teils der Sicht und die Angabe der maximalen Wartezeit, die der Benutzer tolerieren soll. Diese Option stellt nicht sicher, dass die aktuellsten Datensätze zurückgegeben werden, sondern sollte immer leistungsfähiger sein als das Abfragen der gesamten Sicht. Diese Funktion ist nützlich für Szenarien, in denen Sie bereit sind, eine gewisse Aktualität der Leistung zu Opfern, z. b. für telemetriedashboards.

Die Sicht kann an datenbankübergreifenden oder datenbankübergreifenden Abfragen teilnehmen, aber nicht in Platzhalter-Unions oder-suchen enthalten sein.

### <a name="examples"></a>Beispiele

1. Fragen Sie die gesamte Ansicht ab. Die neuesten Datensätze in der Quell Tabelle sind enthalten:
    
    <!-- csl -->
    ```kusto
    ViewName
    ```

1. Abfragen des materialisierten Teils der Sicht, unabhängig vom Zeitpunkt der letzten Materialisierung. 

    <!-- csl -->
    ```kusto
    materialized_view("ViewName")
    ```
  
## <a name="performance-considerations"></a>Überlegungen zur Leistung

Die wichtigsten Mitwirkenden, die sich auf die materialisierte Ansicht auswirken können, sind:

* **Cluster Ressourcen:** Wie bei allen anderen Prozessen, die auf dem Cluster ausgeführt werden, verbrauchen materialisierte Sichten Ressourcen (CPU, Arbeitsspeicher) vom Cluster. Wenn der Cluster überladen ist, kann das Hinzufügen materialisierter Sichten zu einer Verschlechterung der Leistung des Clusters führen. Überwachen Sie die Integrität Ihres Clusters mithilfe von [clusterintegritätsmetriken](../../../using-metrics.md#cluster-metrics) Die [optimierte automatische Skalierung](../../../manage-cluster-horizontal-scaling.md#optimized-autoscale) übernimmt derzeit keine materialisierten Sichten, die im Rahmen der Regeln für die automatische Skalierung berücksichtigt werden.

* **Überlappung mit materialisierten Daten:** Während der Materialisierung werden alle neuen Datensätze, die seit der letzten Materialisierung (Delta) in die Quell Tabelle aufgenommen wurden, verarbeitet und in die Sicht materialisiert. Je höher die Schnittmenge zwischen New-Records und bereits materialisierte-Records ist, desto schlechter ist die Leistung der materialisierten Sicht. Eine materialisierte Sicht funktioniert am besten, wenn die Anzahl der zu aktualisierenden Datensätze (z. b `arg_max` . in der Ansicht) eine kleine Teilmenge der Quell Tabelle ist. Wenn alle oder die meisten der materialisierten Ansichts Datensätze in jedem Materialisierungs Intervall aktualisiert werden müssen, wird die materialisierte Sicht nicht ordnungsgemäß ausgeführt. Verwenden Sie [Blöcke Rebuild Metriken](../../../using-metrics.md#materialized-view-metrics) , um diese Situation zu identifizieren.

* Erfassungs **Rate:** Es gibt keine hart codierten Limits für das Datenvolumen oder die Erfassungs Rate in der Quell Tabelle der materialisierten Sicht. Die empfohlene Erfassungs Rate für materialisierte Sichten beträgt jedoch nicht mehr als 1-2 GB/Sek. Höhere Erfassungs Raten können weiterhin eine gute Leistung haben. Die Leistung hängt von der Clustergröße, den verfügbaren Ressourcen und der Menge der Schnittmenge mit vorhandenen Daten ab.

* **Anzahl der materialisierten Sichten im Cluster:** Die obigen Überlegungen gelten für jede einzelne materialisierte Sicht, die im Cluster definiert ist. Jede Ansicht beansprucht ihre eigenen Ressourcen, und viele Ansichten konkurrieren miteinander in den verfügbaren Ressourcen. Es gibt keine hart codierten Grenzwerte für die Anzahl der materialisierten Sichten in einem Cluster. Die allgemeine Empfehlung besteht jedoch darin, höchstens 10 materialisierte Sichten in einem Cluster zu haben. Die [Kapazitäts Richtlinie](../capacitypolicy.md#materialized-views-capacity-policy) kann angepasst werden, wenn im Cluster mehr als eine einzelne materialisierte Sicht definiert ist.

* **Materialisierte Sicht Definition**: die materialisierte Sicht Definition muss gemäß den bewährten Methoden für Abfragen für eine optimale Abfrageleistung definiert werden. Weitere Informationen finden Sie unter [Erstellen von Tipps zur Leistungssteigerung](materialized-view-create.md#performance-tips).

## <a name="materialized-views-policies"></a>Materialisierte Sichten-Richtlinien

Sie können die [Beibehaltungs Richtlinie](../retentionpolicy.md) und die [Cachingrichtlinie](../cachepolicy.md) einer materialisierten Sicht wie jede beliebige Azure Daten-Explorer Tabelle definieren.

Die materialisierte Sicht leitet die Daten Bank Beibehaltungs-und Cache Richtlinien standardmäßig ab. Die Richtlinien können mithilfe von [Beibehaltungs Richtlinien-Steuerungs Befehlen](../retention-policy.md) oder zwischen [Speichern von Richtlinien Steuerungs Befehlen](../cache-policy.md)geändert werden.
   
   * Die Beibehaltungs Richtlinie der materialisierten Sicht steht nicht im Zusammenhang mit der Beibehaltungs Richtlinie der Quell Tabelle.
   * Wenn die Datensätze der Quell Tabelle nicht anderweitig verwendet werden, kann die Beibehaltungs Richtlinie der Quell Tabelle auf ein Mindestmaß gelöscht werden. Die materialisierte Sicht speichert die Daten weiterhin gemäß der für die Sicht festgelegten Beibehaltungs Richtlinie. 
   * Während sich materialisierte Sichten im Vorschaumodus befinden, empfiehlt es sich, mindestens sieben Tage und die Wiederherstellbarkeit auf true festzulegen. Diese Einstellung ermöglicht eine schnelle Wiederherstellung von Fehlern und zu Diagnose Zwecken.
    
> [!NOTE]
> Keine Aufbewahrungs Richtlinie für die Quell Tabelle wird derzeit nicht unterstützt.

## <a name="materialized-views-monitoring"></a>Überwachung von materialisierten Sichten

Überwachen Sie die Integrität der materialisierten Sicht auf folgende Weise:

* Überwacht [materialisierte Ansichts Metriken](../../../using-metrics.md#materialized-view-metrics) in der Azure-Portal.
* Überwachen Sie die `IsHealthy` von zurückgegebene Eigenschaft [`.show materialized-view`](materialized-view-show-commands.md#show-materialized-view) .
* Überprüfen Sie mithilfe von auf Fehler [`.show materialized-view failures`](materialized-view-show-commands.md#show-materialized-view-failures) .

> [!NOTE]
> Die Materialisierung überspringt niemals Daten, selbst wenn Konstante Fehler auftreten. Die Ansicht gibt immer die aktuellste Momentaufnahme der Abfrage zurück, die auf allen Datensätzen in der Quell Tabelle basiert. Die Abfrageleistung wird durch konstante Ausfälle erheblich beeinträchtigt, aber in Sicht Abfragen werden keine falschen Ergebnisse verursacht.

### <a name="track-resource-consumption"></a>Nachverfolgen des Ressourcenverbrauchs

**Materialisierte Sichten Ressourcenverbrauch:** die vom Materialisierungs Prozess für materialisierte Sichten genutzten Ressourcen können mithilfe des-Befehls nachverfolgt werden [`.show commands-and-queries`](../commands-and-queries.md#show-commands-and-queries) . Filtern Sie die Datensätze für eine bestimmte Sicht mithilfe der folgenden (ersetzen Sie `DatabaseName` und `ViewName` ):

<!-- csl -->
```
.show commands-and-queries 
| where Database  == "DatabaseName" and ClientActivityId startswith "DN.MaterializedViews;ViewName;"
```

### <a name="troubleshooting-unhealthy-materialized-views"></a>Problembehandlung für fehlerhafte materialisierte Sichten

Die `MaterializedViewHealth` Metrik gibt an, ob eine materialisierte Sicht fehlerfrei ist. Eine materialisierte Sicht kann aus einem oder allen der folgenden Gründe fehlerhaft werden:
* Fehler beim Materialisierungs Prozess.
* Der Cluster verfügt nicht über genügend Kapazität, um alle eingehenden Daten zu einem Zeitpunkt zu materialisieren. Fehler bei der Ausführung. Die Ansicht ist jedoch weiterhin fehlerhaft, da Sie im Rückstand bleibt und nicht in der Lage ist, die Erfassungs Rate aufrechtzuerhalten.

Bevor eine materialisierte Sicht fehlerhaft wird, erhöht sich das Alter, das durch die Metrik angegeben wird, `MaterializedViewAgeMinutes` allmählich.

### <a name="troubleshooting-examples"></a>Beispiele zur Problembehandlung

Die folgenden Beispiele helfen Ihnen, fehlerhafte Sichten zu diagnostizieren und zu beheben:

* **Fehler:** Die Quell Tabelle wurde geändert oder gelöscht, die Sicht wurde nicht auf festgelegt `autoUpdateSchema` , oder die Änderung in der Quell Tabelle wird für automatische Updates nicht unterstützt. <br>
   **Diagnose:**  Eine `MaterializedViewResult` Metrik wird ausgelöst, und die `Result` Dimension wird auf festgelegt `SourceTableSchemaChange` / `SourceTableNotFound` .

* **Fehler:** Der Materialisierungs Prozess kann aufgrund unzureichender Cluster Ressourcen nicht ausgeführt werden, und es werden Abfrage Limits getroffen. <br>
  **Diagnose:** `MaterializedViewResult` `Result` die metrikdimension ist auf festgelegt `InsufficientResources` . Azure Daten-Explorer versucht, die automatische Wiederherstellung nach diesem Status auszuführen, sodass dieser Fehler vorübergehend auftreten kann. Wenn die Ansicht jedoch fehlerhaft ist und dieser Fehler ständig ausgegeben wird, ist es möglich, dass die Konfiguration des aktuellen Clusters nicht in der Lage ist, die Erfassungs Rate aufrechtzuerhalten, und dass der Cluster zentral hoch-oder herunterskaliert werden muss.

* **Fehler:** Der Materialisierungs Vorgang ist aufgrund eines anderen (unbekannten) Fehlers nicht möglich. <br> 
   **Diagnose**: `MaterializedViewResult` die Metrik ist `Result` `UnknownError` . Wenn dieser Fehler häufig auftritt, öffnen Sie ein Support Ticket für das Azure Daten-Explorer-Team, um weitere Informationen zu erhalten.

Wenn keine Materialisierungs Fehler auftreten, `MaterializedViewResult` wird die Metrik bei jeder erfolgreichen Ausführung mit ausgelöst `Result` = `Success` . Eine materialisierte Sicht kann trotz erfolgreicher Ausführungen fehlerhaft sein, wenn Sie hinterherhinkt ( `Age` liegt über dem Schwellenwert). Diese Situation kann in den folgenden Situationen auftreten:
   * Die Materialisierung ist langsam, da zu viele Blöcke für die Neuerstellung in jedem Materialisierungs Zyklen vorhanden sind. Weitere Informationen zu den Ursachen für die Neuerstellung von Blöcke, die sich auf die Leistung der Sicht auswirken, finden Sie unter [Funktionsweise materialisierter Sichten](#how-materialized-views-work). 
   * Wenn jeder materialisierungsprozess fast 100% der Blöcke in der Ansicht neu erstellt werden muss, wird die Ansicht möglicherweise nicht beibehalten und ist fehlerhaft. Die Anzahl der Blöcke, die in jedem Cycle neu erstellt werden, wird in der `MaterializedViewExtentsRebuild` Metrik bereitgestellt. In diesem Fall kann es auch hilfreich sein, die in der [materialisierten Ansichts Kapazitäts Richtlinie](../capacitypolicy.md#materialized-views-capacity-policy) neu erstellte Parallelität zu erhöhen. 
   * Im Cluster sind zusätzliche materialisierte Sichten vorhanden, und der Cluster verfügt nicht über genügend Kapazität, um alle Sichten auszuführen. Weitere Informationen finden Sie unter [materialisierte Sicht Kapazitäts Richtlinie](../capacitypolicy.md#materialized-views-capacity-policy) zum Ändern der Standardeinstellungen für die Anzahl der gleichzeitig ausgeführten materialisierten Sichten.

## <a name="next-steps"></a>Nächste Schritte

* [`.create materialized view`](materialized-view-create.md)
* [`.alter materialized-view`](materialized-view-alter.md)
* [Materialisierte Sichten zeigen Befehle an](materialized-view-show-commands.md)
