---
title: 'Kapazitäts Richtlinie: Azure-Daten-Explorer'
description: In diesem Artikel wird die Kapazitäts Richtlinie in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 6d85b5cbf6752924bbaa03d2b81732cd69be6239
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321623"
---
# <a name="capacity-policy"></a>Kapazitätsrichtlinie 

Eine Kapazitäts Richtlinie wird zum Steuern der computeressourcen von Daten Verwaltungs Vorgängen auf dem Cluster verwendet.

## <a name="the-capacity-policy-object"></a>Das Kapazitäts Richtlinien Objekt

Die Kapazitäts Richtlinie besteht aus:

* [Ingestioncapacity](#ingestion-capacity)
* [Extentsmergecapacity](#extents-merge-capacity)
* [Extentspurgerebuildcapacity](#extents-purge-rebuild-capacity)
* [Exportcapacity](#export-capacity)
* [Extentspartitioncapacity](#extents-partition-capacity)

## <a name="ingestion-capacity"></a>Erfassungs Kapazität

|Eigenschaft       |type    |Beschreibung    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------|
|Clustermaximumconcurrentoperations |long    |Ein maximaler Wert für die Anzahl von gleichzeitigen Erfassungs Vorgängen in einem Cluster.               |
|Coreutilizationkoeffizienten         |double  |Ein Koeffizienten für den Prozentsatz der Kerne, die beim Berechnen der Erfassungs Kapazität verwendet werden sollen. Das Ergebnis der Berechnung wird immer von normalisiert. `ClusterMaximumConcurrentOperations` <br> Die Gesamt Erfassungs Kapazität des Clusters, wie von angezeigt [. zeigen](../management/diagnostics.md#show-capacity)Sie die Kapazität an: <br> Minimal ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * Maximum (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> In Clustern mit drei oder mehr Knoten ist der Administrator Knoten nicht Teil der Erfassungs Vorgänge. Der `Number of nodes in cluster` wird um 1 reduziert.

## <a name="extents-merge-capacity"></a>Erweitert die Zusammenfassungs Kapazität

|Eigenschaft                           |type    |Beschreibung                                                                                                |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------------------|
|Minimumconcurrentoperationspernode |long    |Ein Minimalwert für die Anzahl der gleichzeitigen Vorgänge zum Zusammenführen und Neuerstellen von Blöcken auf einem einzelnen Knoten. Der Standardwert ist 1 |
|Maximumconcurrentoperationspernode |long    |Ein maximaler Wert für die Anzahl der gleichzeitigen Vorgänge zum Zusammenführen und Neuerstellen von Blöcken auf einem einzelnen Knoten. Der Standardwert ist 3 |

Die Gesamt Zusammenfassungs Kapazität des Clusters, wie von gezeigt, [`.show capacity`](../management/diagnostics.md#show-capacity) wird berechnet durch:

`Number of nodes in cluster` Stuben `Concurrent operations per node`

Der effektive Wert für `Concurrent operations per node` wird automatisch vom System im Bereich [ `MinimumConcurrentOperationsPerNode` , `MaximumConcurrentOperationsPerNode` ] angepasst, solange die Erfolgsrate der Mergevorgänge über 90% liegt.

> [!Note]
> In Clustern mit drei oder mehr Knoten ist der Administrator Knoten nicht an der Ausführung von Merge-Vorgängen beteiligt. Der `Number of nodes in cluster` wird um 1 reduziert.

## <a name="extents-purge-rebuild-capacity"></a>Erweiterbare Lösch Kapazität für Lösch Blöcke

|Eigenschaft                           |type    |Beschreibung                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|Maximumconcurrentoperationspernode |long    |Ein maximaler Wert für die Anzahl der gleichzeitigen Rebuild-Blöcke für Löschvorgänge auf einem einzelnen Knoten. |

Die Gesamtanzahl der Blöcke zum Löschen von Blöcken im Cluster Gesamt (wie von gezeigt [`.show capacity`](../management/diagnostics.md#show-capacity) ) wird berechnet durch:

`Number of nodes in cluster` Stuben `MaximumConcurrentOperationsPerNode`

> [!Note]
> In Clustern mit drei oder mehr Knoten ist der Administrator Knoten nicht an der Ausführung von Merge-Vorgängen beteiligt. Der `Number of nodes in cluster` wird um 1 reduziert.

## <a name="export-capacity"></a>Exportieren von Kapazität

|Eigenschaft                           |type    |Beschreibung                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|Clustermaximumconcurrentoperations |long    |Ein maximaler Wert für die Anzahl von gleichzeitigen Export Vorgängen in einem Cluster.                                           |
|Coreutilizationkoeffizienten         |double  |Ein Koeffizienten für den Prozentsatz der Kerne, die beim Berechnen der Exportkapazität verwendet werden sollen. Das Ergebnis der Berechnung wird immer von normalisiert `ClusterMaximumConcurrentOperations` . |

Die Gesamt Exportkapazität des Clusters, wie von gezeigt [`.show capacity`](../management/diagnostics.md#show-capacity) , wird berechnet durch:

Minimal ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * Maximum (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> In Clustern mit drei oder mehr Knoten ist der Administrator Knoten nicht an Export Vorgängen beteiligt. Der `Number of nodes in cluster` wird um 1 reduziert.

## <a name="extents-partition-capacity"></a>Erweitert die Partitions Kapazität

|Eigenschaft                           |type    |Beschreibung                                                                                         |
|-----------------------------------|--------|----------------------------------------------------------------------------------------------------|
|Clusterminimumconcurrentoperations |long    |Ein Minimalwert für die Anzahl der gleichzeitigen Erweiterungs Vorgänge in einem Cluster. Standardwert: 1  |
|Clustermaximumconcurrentoperations |long    |Ein maximaler Wert für die Anzahl der gleichzeitigen Erweiterungs Vorgänge in einem Cluster. Standardwert: 16 |

Die Gesamtwerte für die Partitions Kapazität des Clusters (wie von dargestellt [`.show capacity`](../management/diagnostics.md#show-capacity) ).

Der effektive Wert für `Concurrent operations` wird automatisch vom System im Bereich [ `ClusterMinimumConcurrentOperations` , `ClusterMaximumConcurrentOperations` ] angepasst, solange die Erfolgsrate der Partitionierungs Vorgänge über 90% liegt.

## <a name="materialized-views-capacity-policy"></a>Kapazitäts Richtlinie für materialisierte Sichten

Ändern Sie die Kapazitäts Richtlinie mithilfe der [Alter Cluster Policy-Kapazität](capacity-policy.md#alter-cluster-policy-capacity). Diese Änderung erfordert `AllDatabasesAdmin` Berechtigungen.
Die Richtlinie kann verwendet werden, um Parallelitäts Einstellungen für materialisierte Sichten zu ändern. Diese Änderung ist möglicherweise erforderlich, wenn mehr als eine einzelne materialisierte Sicht in einem Cluster definiert ist und der Cluster nicht mit der Materialisierung aller Sichten Schritt halten kann. Standardmäßig sind Parallelitäts Einstellungen relativ niedrig, um sicherzustellen, dass die Materialisierung die Leistung des Clusters nicht beeinträchtigt.

> [!WARNING]
> Die materialisierte Ansichts Kapazität sollte nur gesteigert werden, wenn die Ressourcen des Clusters gut sind (geringe CPU, verfügbarer Arbeitsspeicher). Wenn Sie diese Werte erhöhen, wenn Ressourcen eingeschränkt sind, kann dies zu einer Erschöpfung der Ressourcen führen, was die Leistung des Clusters beeinträchtigt.

Die Kapazitäts Richtlinie materialisierte Sichten ist Teil der [Kapazitäts Richtlinie](#capacity-policy)des Clusters und verfügt über die folgende JSON-Darstellung:

<!-- csl -->
``` 
{
   "MaterializedViewsCapacity": {
    "ClusterMaximumConcurrentOperations": 1,
    "ExtentsRebuildCapacity": {
      "ClusterMaximumConcurrentOperations": 50,
      "MaximumConcurrentOperationsPerNode": 5
    }
  }
}
```

### <a name="properties"></a>Eigenschaften

Eigenschaft | Beschreibung
|---|---|
|`ClusterMaximumConcurrentOperations` | Die maximale Anzahl von materialisierten Sichten, die der Cluster gleichzeitig materialisieren kann. Dieser Wert ist standardmäßig 1, während Materialisierung selbst (einer einzelnen Ansicht) viele gleichzeitige Vorgänge ausführen kann. Wenn im Cluster mehr als eine einzelne materialisierte Sicht definiert ist und sich die Ressourcen des Clusters in einem guten Zustand befinden, wird empfohlen, diesen Wert zu erhöhen. |
| `ExtentsRebuildCapacity`|  Bestimmt die Anzahl von gleichzeitigen Blöcken zum erneuten Erstellen, die für alle materialisierten Sichten während des Materialisierungs Vorgangs ausgeführt werden. Wenn mehrere Sichten gleichzeitig ausgeführt werden, da `ClusterMaximumConcurrentOperation` größer als 1 ist, wird das von dieser Eigenschaft definierte Kontingent gemeinsam genutzt. Der Wert für die maximale Anzahl von gleichzeitigen Blöcke wird nicht überschritten. |

### <a name="extents-rebuild"></a>Extents Rebuild

Weitere Informationen zu Blöcken zum Erstellen von Blöcken finden Sie unter [Funktionsweise von materialisierten Sichten](materialized-views/materialized-view-overview.md#how-materialized-views-work). Die maximale Anzahl von Blöcke, die erweitert werden, wird berechnet durch:
    
```kusto
Maximum(`ClusterMaximumConcurrentOperations`, `Number of nodes in cluster` * `MaximumConcurrentOperationsPerNode`)
```

* Die Standardwerte sind 50 Gesamtzahl der Parallelitäts neubuilds und maximal 5 pro Knoten.
* Die `ExtentsRebuildCapacity` Richtlinie dient nur als Obergrenze. Der tatsächlich verwendete Wert wird vom System dynamisch festgelegt, basierend auf den Bedingungen des aktuellen Clusters (Arbeitsspeicher, CPU) und einer Schätzung der Menge an Ressourcen, die für den Rebuild-Vorgang erforderlich sind. In der Praxis kann die Parallelität wesentlich niedriger sein als der in Capacity Policy angegebene Wert.
    * Die `MaterializedViewExtentsRebuild` Metrik enthält Informationen darüber, wie viele Blöcke in jedem Materialisierungs Intervall neu erstellt wurden. Weitere Informationen finden Sie unter [materialisierte Ansichten Überwachung](materialized-views/materialized-view-overview.md#materialized-views-monitoring).

## <a name="defaults"></a>Standardeinstellungen

Die standardmäßige Kapazitäts Richtlinie weist die folgende JSON-Darstellung auf:

```json
{
  "IngestionCapacity": {
    "ClusterMaximumConcurrentOperations": 512,
    "CoreUtilizationCoefficient": 0.75
  },
  "ExtentsMergeCapacity": {
    "MinimumConcurrentOperationsPerNode": 1,
    "MaximumConcurrentOperationsPerNode": 3
  },
  "ExtentsPurgeRebuildCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExportCapacity": {
    "ClusterMaximumConcurrentOperations": 100,
    "CoreUtilizationCoefficient": 0.25
  },
  "ExtentsPartitionCapacity": {
    "ClusterMinimumConcurrentOperations": 1,
    "ClusterMaximumConcurrentOperations": 16
  }
}
```

## <a name="control-commands"></a>Steuerungsbefehle

> [!WARNING]
> Wenden Sie sich an das Azure Daten-Explorer-Team, bevor Sie eine Kapazitäts Richtlinie ändern.

* Verwenden [`.show cluster policy capacity`](capacity-policy.md#show-cluster-policy-capacity) Sie, um die aktuelle Kapazitäts Richtlinie des Clusters anzuzeigen.

* Verwenden [`.alter cluster policy capacity`](capacity-policy.md#alter-cluster-policy-capacity) Sie, um die Kapazitäts Richtlinie des Clusters zu ändern.

## <a name="throttling"></a>Drosselung

Kusto schränkt die Anzahl gleichzeitiger Anforderungen für die folgenden vom Benutzer initiierten Befehle ein:

* Ingestionen (enthält alle [hier](../../ingest-data-overview.md)aufgeführten Befehle)
   * Der Grenzwert wird in der [Kapazitäts Richtlinie](#capacity-policy)definiert.
* Löscht
   * Global ist derzeit auf einem Cluster pro Cluster korrigiert.
   * Die Kapazität zum Löschen von Lösch Vorgängen wird intern verwendet, um die Anzahl gleichzeitiger Neuerstellung während der Lösch Befehle zu bestimmen. Lösch Befehle werden aufgrund dieses Vorgangs nicht blockiert/gedrosselt, Sie funktionieren jedoch je nach Lösch Kapazität für Löschvorgänge schneller oder langsamer.
* Exports
   * Der Grenzwert wird in der [Kapazitäts Richtlinie](#capacity-policy)definiert.

Wenn der Cluster erkennt, dass ein Vorgang den zulässigen gleichzeitigen Vorgang überschritten hat, antwortet er mit einem 429, "gedrosselt", HTTP-Code.
Wiederholen Sie den Vorgang nach einigen Backoff.
