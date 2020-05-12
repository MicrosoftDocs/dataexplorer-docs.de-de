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
ms.openlocfilehash: 15a1c21a38999b0a3929fcf0451a91ec607ca2a8
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225920"
---
# <a name="capacity-policy"></a>Kapazitätsrichtlinie

Eine Kapazitäts Richtlinie wird verwendet, um die computeressourcen zu steuern, die für Daten Verwaltungsvorgänge im Cluster verwendet werden.

## <a name="the-capacity-policy-object"></a>Das Kapazitäts Richtlinien Objekt

Die Kapazitäts Richtlinie besteht aus:

* [Ingestioncapacity](#ingestion-capacity)
* [Extentsmergecapacity](#extents-merge-capacity)
* [Extentspurgerebuildcapacity](#extents-purge-rebuild-capacity)
* [Exportcapacity](#export-capacity)
* [Extentspartitioncapacity](#extents-partition-capacity)

## <a name="ingestion-capacity"></a>Erfassungs Kapazität

|Eigenschaft                           |type    |Beschreibung                                                                                                                                                                               |
|-----------------------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|Clustermaximumconcurrentoperations |long    |Ein maximaler Wert für die Anzahl von gleichzeitigen Erfassungs Vorgängen in einem Cluster.                                                                                                            |
|Coreutilizationkoeffizienten         |double  |Ein Koeffizienten für den Prozentsatz der Kerne, die beim Berechnen der Erfassungs Kapazität verwendet werden sollen (das Ergebnis der Berechnung wird immer von normalisiert `ClusterMaximumConcurrentOperations` ). |                                                                                                                             |

Die Gesamt Erfassungs Kapazität des Clusters (wie von angezeigt [. Anzeige Kapazität](../management/diagnostics.md#show-capacity)) wird anhand der folgenden Schritte berechnet:

Minimal ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * Maximum (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> In Clustern mit drei oder mehr Knoten ist der Administrator Knoten nicht Teil der Erfassungs Vorgänge. Der `Number of nodes in cluster` wird um 1 reduziert.

## <a name="extents-merge-capacity"></a>Erweitert die Zusammenfassungs Kapazität

|Eigenschaft                           |type    |Beschreibung                                                                                    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------|
|Maximumconcurrentoperationspernode |long    |Ein maximaler Wert für die Anzahl der gleichzeitigen Vorgänge zum Zusammenführen und Neuerstellen von Blöcken auf einem einzelnen Knoten. |

Die Gesamt Zusammenfassungs Kapazität des Clusters (wie von angezeigt [. Show Capacity](../management/diagnostics.md#show-capacity)) wird berechnet durch:

`Number of nodes in cluster`Stuben`MaximumConcurrentOperationsPerNode`

> [!Note]
> * `MaximumConcurrentOperationsPerNode`wird automatisch vom System im Bereich [1, 5] angepasst.
> * In Clustern mit drei oder mehr Knoten ist der Administrator Knoten nicht an der Ausführung von Merge-Vorgängen beteiligt. Der `Number of nodes in cluster` wird um 1 reduziert.

## <a name="extents-purge-rebuild-capacity"></a>Erweiterbare Lösch Kapazität für Lösch Blöcke

|Eigenschaft                           |type    |Beschreibung                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|Maximumconcurrentoperationspernode |long    |Ein maximaler Wert für die Anzahl der gleichzeitigen Rebuild-Blöcke für Löschvorgänge auf einem einzelnen Knoten. |

Die Gesamtanzahl der Blöcke zum Löschen von Blöcken im Cluster Gesamt (wie von angezeigt [. Anzeige Kapazität](../management/diagnostics.md#show-capacity)) wird berechnet durch:

`Number of nodes in cluster`Stuben`MaximumConcurrentOperationsPerNode`

> [!Note]
> In Clustern mit drei oder mehr Knoten ist der Administrator Knoten nicht an der Ausführung von Merge-Vorgängen beteiligt. Der `Number of nodes in cluster` wird um 1 reduziert.

## <a name="export-capacity"></a>Exportieren von Kapazität

|Eigenschaft                           |type    |Beschreibung                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|Clustermaximumconcurrentoperations |long    |Ein maximaler Wert für die Anzahl von gleichzeitigen Export Vorgängen in einem Cluster.                                                                                                           |
|Coreutilizationkoeffizienten         |double  |Ein Koeffizienten für den Prozentsatz der Kerne, die beim Berechnen der Exportkapazität verwendet werden sollen. Das Ergebnis der Berechnung wird immer von normalisiert `ClusterMaximumConcurrentOperations` . |

Die Gesamt Exportkapazität des Clusters (wie von angezeigt [. Anzeige Kapazität](../management/diagnostics.md#show-capacity)) wird berechnet durch:

Minimal ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * Maximum (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> In Clustern mit drei oder mehr Knoten ist der Administrator Knoten nicht an der Ausführung von Export Vorgängen beteiligt. Der `Number of nodes in cluster` wird um 1 reduziert.

## <a name="extents-partition-capacity"></a>Erweitert die Partitions Kapazität

|Eigenschaft                           |type    |Beschreibung                                                                             |
|-----------------------------------|--------|----------------------------------------------------------------------------------------|
|Clustermaximumconcurrentoperations |long    |Ein maximaler Wert für die Anzahl der gleichzeitigen Erweiterungs Vorgänge in einem Cluster. |

Die gesamte Partitions Kapazität des Clusters (wie von angezeigt [. Show Capacity](../management/diagnostics.md#show-capacity)) wird durch eine einzelne Eigenschaft definiert: `ClusterMaximumConcurrentOperations` .

> [!Note]
> `ClusterMaximumConcurrentOperations`wird automatisch vom System im Bereich [1, 10] angepasst.

## <a name="defaults"></a>Standardeinstellungen

Die standardmäßige Kapazitäts Richtlinie weist die folgende JSON-Darstellung auf:

```kusto 
{
  "IngestionCapacity": {
    "ClusterMaximumConcurrentOperations": 512,
    "CoreUtilizationCoefficient": 0.75
  },
  "ExtentsMergeCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExtentsPurgeRebuildCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExportCapacity": {
    "ClusterMaximumConcurrentOperations": 100,
    "CoreUtilizationCoefficient": 0.25
  }
}
```

## <a name="control-commands"></a>Steuerungsbefehle

> [!WARNING]
> Es wird nur selten empfohlen, eine Kapazitäts Richtlinie aufgrund der möglichen Auswirkungen auf die verfügbaren Ressourcen des Clusters zu ändern.

* Verwenden Sie [. zeigen Sie die Kapazität der Cluster Richtlinie](capacity-policy.md#show-cluster-policy-capacity) an, um die aktuelle Kapazitäts Richtlinie des Clusters anzuzeigen.

* Ändern Sie die Kapazitäts Richtlinie des Clusters mithilfe von [. Alter Cluster Policy Capacity](capacity-policy.md#alter-cluster-policy-capacity) .

## <a name="throttling"></a>Drosselung

Kusto schränkt die Anzahl gleichzeitiger Anforderungen für die folgenden vom Benutzer initiierten Befehle ein:

* Ingestionen (enthält alle [hier](../management/data-ingestion/index.md)aufgeführten Befehle)
   * Der Grenzwert wird in der [Kapazitäts Richtlinie](#capacity-policy)definiert.
* Löscht
   * Global ist derzeit auf einem Cluster pro Cluster korrigiert.
   * Die Kapazität zum Löschen von Lösch Vorgängen wird intern verwendet, um die Anzahl gleichzeitiger Neuerstellung während der Lösch Befehle zu bestimmen. Lösch Befehle werden aufgrund dieses Vorgangs nicht blockiert/gedrosselt, Sie funktionieren jedoch je nach Lösch Kapazität für Löschvorgänge schneller oder langsamer.
* Exports
   * Der Grenzwert wird in der [Kapazitäts Richtlinie](#capacity-policy)definiert.

Wenn der Cluster erkennt, dass ein Vorgang den zulässigen gleichzeitigen Vorgang überschritten hat, antwortet er mit einem 429-HTTP-Code ("Throttled").
Wiederholen Sie den Vorgang nach einigen Backoff.
