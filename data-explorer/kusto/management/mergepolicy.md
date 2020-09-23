---
title: 'Extents-Merge-Richtlinie: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Extents-Zusammenschluss Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 87980046e6f0ebbbdd17a9037aa1206779d2a61e
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057035"
---
# <a name="merge-policy"></a>Zusammenführungsrichtlinie

Die Merge-Richtlinie definiert, ob und wie [Blöcke (Daten-Shards)](../management/extents-overview.md) im Kusto-Cluster zusammengeführt werden sollen.

Es gibt zwei Typen von Merge-Vorgängen: `Merge` , die Indizes neu erstellen, und `Rebuild` , wodurch die Daten vollständig neu erstellt werden.

Beide Vorgangs Typen führen zu einem einzelnen Block, der die Quell Blöcke ersetzt.

Standardmäßig werden Rebuild-Vorgänge bevorzugt. Wenn Blöcke vorhanden sind, die nicht den Kriterien für die Neuerstellung entsprechen, wird versucht, Sie zusammenzuführen.  

> [!NOTE]
> * Das Markieren von Blöcken mit *unterschiedlichen* `drop-by` Tags bewirkt, dass solche Blöcke nicht zusammengeführt werden, auch wenn eine Merge-Richtlinie festgelegt wurde. Weitere Informationen finden Sie Unterblock- [Tagging](../management/extents-overview.md#extent-tagging).
> * Blöcke, deren Union-Tags die Länge von 1 Mio. Zeichen überschreiten, werden nicht zusammengeführt.
> * Die [Sharding-Richtlinie](./shardingpolicy.md) der Datenbank oder der Tabelle wirkt sich auch darauf aus, wie Blöcke zusammengeführt werden.

## <a name="merge-policy-properties"></a>Richtlinien Eigenschaften für Zusammenführen

Die Merge-Richtlinie enthält die folgenden Eigenschaften:

* **Rowzähltupperboundformerge**:
    * Standardwerte:
      * 0 (unbegrenzt) für Richtlinien, die vor dem 2020. Juni festgelegt wurden.
      * 16 Millionen für Richtlinien, die ab dem 2020. Juni festgelegt wurden.
    * Maximal zulässige Zeilen Anzahl des zusammengeführten Wertebereichs.
    * Gilt für Merge-Vorgänge, nicht für die erneute Erstellung.  
* **Originalsizembupperboundformerge**:
    * Der Standardwert ist 0 (unbegrenzt).
    * Die maximal zulässige ursprüngliche Größe (in MB) des zusammengeführten Wertebereichs.
    * Gilt für Merge-Vorgänge, nicht für die erneute Erstellung.  
* **Maxextentstomerge**:
    * Der Standardwert ist 100.
    * Maximal zulässige Anzahl von Blöcken, die in einem einzelnen Vorgang zusammengeführt werden sollen.
    * Gilt für Merge-Vorgänge.
* **Schleifen Zeitraum**:
    * Der Standardwert ist 01:00:00 (1 Stunde).
    * Die maximale Wartezeit zwischen dem Starten von zwei aufeinander folgenden Iterationen von Merge-oder Rebuild-Vorgängen durch den Datenverwaltung-Dienst.
    * Gilt für Merge-und Rebuild-Vorgänge.
* **Zuordnung**:
    * Der Standardwert ist "true".
    * Definiert `Rebuild` , ob Vorgänge aktiviert sind (in diesem Fall werden Sie gegenüber `Merge` Vorgängen bevorzugt).
* **AllowMerge**:
    * Der Standardwert ist "true".
    * Definiert `Merge` , ob Vorgänge aktiviert werden. in diesem Fall werden Sie weniger bevorzugt als `Rebuild` Vorgänge.
* **Maxrangeingehours**:
    * Der Standardwert ist 8.
        * Der Standardwert ist 14 Tage in [materialisierten Sichten](materialized-views/materialized-view-overview.md), es sei denn, die Wiederherstellbarkeit ist in der effektiven [Beibehaltungs Richtlinie](retentionpolicy.md)der materialisierten Sichten deaktiviert.
    * Maximal zulässige Differenz (in Stunden) zwischen den Erstellungs Zeiten zweier verschiedener Blöcke, sodass Sie weiterhin gemergt werden können.
    * Timestamps sind von der Block Erstellung und stehen nicht in Beziehung zu den tatsächlichen Daten, die in den Blöcken enthalten sind.
    * Gilt für Merge-und Rebuild-Vorgänge.
    * Dieser Wert sollte gemäß den Werten für die effektive [Beibehaltungs Richtlinie](./retentionpolicy.md) " *Software Update Period*" oder " [Cache Policy](./cachepolicy.md) *datahotspan* " festgelegt werden. Nehmen Sie den niedrigeren Wert von " *Soft-teperiod* " und " *datahotspan*" an. Legen Sie den Wert für *maxrangeinhours* auf einen Wert zwischen 2-3% fest. Sehen Sie sich die [Beispiele](#maxrangeinhours-examples) an.

## <a name="maxrangeinhours-examples"></a>Maxrangeingehours-Beispiele

|min (Software Update Period (Aufbewahrungs Richtlinie), datahotspan (Cache Richtlinie))|Maximaler Bereich in Stunden (Merge-Richtlinie)|
|--------------------------------------------------------------------|---------------------------------|
|7 Tage (168 Stunden)                                                  | 4                               |
|14 Tage (336 Stunden)                                                 | 8                               |
|30 Tage (720 Stunden)                                                 | 18                              |
|60 Tage (1.440 Stunden)                                               | 36                              |
|90 Tage (2.160 Stunden)                                               | 60                              |
|180 Tage (4.320 Stunden)                                              | 120                             |
|365 Tage (8.760 Stunden)                                              | 250                             |

> [!WARNING]
> Wenden Sie sich an das Azure Daten-Explorer-Team, bevor Sie eine Blöcke-Zusammenführung ändern.

Wenn eine Datenbank erstellt wird, wird Sie mit den oben erwähnten Standardwerten für die Merge-Richtlinie festgelegt. Die Richtlinie wird standardmäßig von allen Tabellen geerbt, die in der Datenbank erstellt wurden, es sei denn, Ihre Richtlinien werden explizit auf Tabellenebene überschrieben.

Weitere Informationen finden Sie unter [Steuern von Befehlen, die Ihnen das Verwalten von Merge-Richtlinien für Datenbanken oder Tabellen ermöglichen](../management/merge-policy.md).
