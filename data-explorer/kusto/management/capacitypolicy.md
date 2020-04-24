---
title: Kapazitätsrichtlinie - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Kapazitätsrichtlinie in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: af648bd0a4b328477b14e20a2457e3e914df2827
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521997"
---
# <a name="capacity-policy"></a>Kapazitätspolitik

Eine Kapazitätsrichtlinie wird verwendet, um die Computeressourcen zu steuern, die zum Ausführen der Datenerfassung und anderer Datenoptimierungsvorgänge (z. B. Zusammenführen von Ausdehnungen) verwendet werden.

## <a name="the-capacity-policy-object"></a>Das Kapazitätsrichtlinienobjekt

Die Kapazitätsrichtlinie besteht `IngestionCapacity` `ExtentsMergeCapacity`aus `ExtentsPurgeRebuildCapacity` `ExportCapacity`, und .

### <a name="ingestion-capacity"></a>Aufnahmekapazität

|Eigenschaft                           |type    |BESCHREIBUNG                                                                                                                                                                               |
|-----------------------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |Maximaler Wert für die Anzahl gleichzeitiger Aufnahmevorgänge in einem Cluster                                                                                                            |
|CoreUtilizationKoeffizient         |double  |Ein Koeffizient für den Prozentsatz der Kerne, der bei der Berechnung der Aufnahmekapazität `ClusterMaximumConcurrentOperations`verwendet werden soll (das Ergebnis der Berechnung wird immer normalisiert durch ) |                                                                                                                             |

Die Gesamtaufnahmekapazität des Clusters (wie durch [.show capacity](../management/diagnostics.md#show-capacity)dargestellt) wird berechnet durch:

Minimum(`ClusterMaximumConcurrentOperations` `Number of nodes in cluster` , * Maximum(1, `Core count per node`  *  `CoreUtilizationCoefficient`))

> [!Note] 
> In Clustern mit drei Knoten oder höher nimmt der Admin-Knoten nicht `Number of nodes in cluster` an der Durchführung von Aufnahmevorgängen teil und wird daher um 1 reduziert.

### <a name="extents-merge-capacity"></a>Extents Merge-Kapazität

|Eigenschaft                           |type    |BESCHREIBUNG                                                                                    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |Maximaler Wert für die Anzahl der gleichzeitigen Ausdehnungen, die Zusammenführungs-/Neuerstellungsvorgänge auf einem einzelnen Knoten ausführen |

Die Gesamterweiterungsmergekapazität des Clusters (wie durch [.show capacity](../management/diagnostics.md#show-capacity)dargestellt) wird berechnet durch:

`Number of nodes in cluster`X`MaximumConcurrentOperationsPerNode`

> [!Note] 
> In Clustern mit drei Knoten oder höher nimmt der Admin-Knoten `Number of nodes in cluster` nicht am Ausführen von Mergevorgängen teil und wird daher um 1 reduziert.

### <a name="extents-purge-rebuild-capacity"></a>Extents Purge Rebuild-Kapazität

|Eigenschaft                           |type    |BESCHREIBUNG                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |Ein maximaler Wert für die Anzahl der gleichzeitigen Erweiterungen, die Neuerstellungsvorgänge (Neuerstellungsausdehnungen für Bereinigungsvorgänge) auf einem einzelnen Knoten bereinigen |

Die Gesamtausdehnungen des Clusters bereinigen die Wiederherstellungskapazität (wie durch [.show-Kapazität](../management/diagnostics.md#show-capacity)dargestellt) wird berechnet durch:

`Number of nodes in cluster`X`MaximumConcurrentOperationsPerNode`

> [!Note] 
> In Clustern mit drei Knoten oder höher nimmt der Admin-Knoten `Number of nodes in cluster` nicht am Ausführen von Mergevorgängen teil und wird daher um 1 reduziert.

### <a name="export-capacity"></a>Exportkapazität

|Eigenschaft                           |type    |BESCHREIBUNG                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |Ein maximaler Wert für die Anzahl der gleichzeitigen Exportvorgänge in einem Cluster.                                                                                                           |
|CoreUtilizationKoeffizient         |double  |Ein Koeffizient für den Prozentsatz der Kerne, der bei der Berechnung der Exportkapazität verwendet werden soll (das Berechnungsergebnis wird immer normalisiert durch `ClusterMaximumConcurrentOperations`) |

Die gesamte Exportkapazität des Clusters (wie durch [.show capacity](../management/diagnostics.md#show-capacity)dargestellt) wird berechnet durch:

Minimum(`ClusterMaximumConcurrentOperations` `Number of nodes in cluster` , * Maximum(1, `Core count per node`  *  `CoreUtilizationCoefficient`))

> [!Note] 
> In Clustern mit drei Knoten oder höher nimmt der Admin-Knoten `Number of nodes in cluster` nicht am Ausführen von Exportvorgängen teil und wird daher um 1 reduziert.

### <a name="extents-partition-capacity"></a>Ausdehnungen Partitionskapazität

|Eigenschaft                           |type    |BESCHREIBUNG                                                                             |
|-----------------------------------|--------|----------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |Ein maximaler Wert für die Anzahl gleichzeitiger Erweiterungen Partitionsvorgänge in einem Cluster. |

Die Gesamterweiterungspartitionskapazität des Clusters (wie durch [.show-Kapazität](../management/diagnostics.md#show-capacity) `ClusterMaximumConcurrentOperations`dargestellt ) wird durch eine einzelne Eigenschaft definiert: .

### <a name="defaults"></a>Standardeinstellungen

Die Standardkapazitätsrichtlinie weist die folgende JSON-Darstellung auf:

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

> [!WARNING]
> Es wird **selten** empfohlen, eine Kapazitätsrichtlinie ohne rücksprache mit dem Kusto-Team zu ändern.

## <a name="control-commands"></a>Steuerbefehle

* Verwenden Sie [.show Clusterrichtlinienkapazität,](capacity-policy.md#show-cluster-policy-capacity) um die aktuelle Kapazitätsrichtlinie des Clusters anzuzeigen.
* Verwenden Sie [.alter Clusterrichtlinienkapazität,](capacity-policy.md#alter-cluster-policy-capacity) um die Kapazitätsrichtlinie des Clusters zu ändern.

## <a name="throttling"></a>Drosselung

Kusto begrenzt die Anzahl der gleichzeitigen Anforderungen für die folgenden Befehle:

1. Einnahme (enthält alle Befehle, die [hier](../management/data-ingestion/index.md)aufgeführt sind)
      * Limit ist wie in der [Kapazitätsrichtlinie](#capacity-policy)definiert.
1. Merges
      * Limit ist wie in der [Kapazitätsrichtlinie](#capacity-policy)definiert.
1. Säuberungen
      * Global ist derzeit auf 1 pro Cluster festgelegt.
      * Die Löschkapazität wird intern verwendet, um die Anzahl der gleichzeitigen Neuerstellungsvorgänge während der Löschbefehle zu bestimmen (Löschbefehle werden daher nicht blockiert/gedrosselt, sondern arbeiten je nach Löschkapazität schneller/langsamer).
1. Exports
      * Limit ist wie in der [Kapazitätsrichtlinie](#capacity-policy)definiert.


Wenn Kusto erkennt, dass ein Vorgang den zulässigen gleichzeitigen Vorgang überschritten hat, antwortet Kusto mit einem 429 HTTP-Code.
Der Client sollte den Vorgang nach einem Backoff wiederholen.