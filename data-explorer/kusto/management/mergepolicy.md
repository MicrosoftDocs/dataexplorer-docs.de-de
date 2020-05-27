---
title: 'Extents-Zusammenschluss Richtlinie: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Extents-Zusammenschluss Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: f0398fbc19842b3cce7fe69c8cb61258d0aeaaa6
ms.sourcegitcommit: a562ce255ac706ca1ca77d272a97b5975235729d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867034"
---
# <a name="extents-merge-policy"></a>Extents-Merge-Richtlinie
Die Merge-Richtlinie definiert, ob und wie [Blöcke (Daten-Shards)](../management/extents-overview.md) im Kusto-Cluster zusammengeführt werden sollen.

Es gibt zwei Arten von Merge-Vorgängen: `Merge` (wodurch Indizes neu erstellt werden) und `Rebuild` (wodurch die Daten vollständig wieder hergestellt werden).

Beide Vorgangs Arten führen zu einem einzelnen Block, der die Quell Blöcke ersetzt.

Standardmäßig werden Neuerstellungs Vorgänge bevorzugt. nur wenn verbleibende Blöcke vorhanden sind, die nicht den Kriterien für die Neuerstellung entsprechen, werden Sie zusammengeführt.  

*Hinweise:*
- Das Markieren von Blöcken mit *verschiedenen* `drop-by` Tags bewirkt, dass solche Blöcke nicht zusammengeführt werden, auch wenn eine mergerichtlinie festgelegt wurde (siehe Block- [Tagging](../management/extents-overview.md#extent-tagging)).
- Blöcke, deren Union-Tags die Länge von 1 Mio. Zeichen überschreiten, werden nicht zusammengeführt.
- Die [Sharding-Richtlinie](./shardingpolicy.md) der Datenbank/Tabelle wirkt sich auch darauf aus, wie Blöcke zusammengeführt werden.

Die Merge-Richtlinie enthält die folgenden Eigenschaften:

- **Rowzähltupperboundformerge**:
    - Standardwerte:
      - 0 (unbegrenzt) für Richtlinien, die vor dem 2020. Juni festgelegt wurden.
      - 16 Millionen für Richtlinien, die ab dem 2020. Juni festgelegt wurden.
    - Maximal zulässige Zeilen Anzahl des zusammengeführten Wertebereichs.
    - Gilt für Merge-Vorgänge, nicht für die erneute Erstellung.  
- **Originalsizembupperboundformerge**:
    - Der Standardwert ist 0 (unbegrenzt).
    - Die maximal zulässige ursprüngliche Größe (in MB) des zusammengeführten Wertebereichs.
    - Gilt für Merge-Vorgänge, nicht für die erneute Erstellung.  
- **Maxextentstomerge**:
    - Der Standardwert ist 100.
    - Maximal zulässige Anzahl von Blöcken, die in einem einzelnen Vorgang zusammengeführt werden sollen.
    - Gilt für Merge-Vorgänge.
- **Schleifen Zeitraum**:
    - Der Standardwert ist 01:00:00 (1 Stunde).
    - Die maximale Wartezeit zwischen dem Starten von zwei aufeinander folgenden Iterationen von Merge/Rebuild-Vorgängen (durch den Datenverwaltung-Dienst ausgeführt).
    - Gilt für Merge-und Rebuild-Vorgänge.
- **Zuordnung**:
    - Der Standardwert ist "true".
    - Definiert `Rebuild` , ob Vorgänge aktiviert sind (in diesem Fall werden Sie gegenüber `Merge` Vorgängen bevorzugt).
- **AllowMerge**:
    - Der Standardwert ist "true".
    - Definiert `Merge` , ob Vorgänge aktiviert sind (in diesem Fall sind Sie weniger bevorzugt als `Rebuild` Vorgänge).
- **Maxrangeingehours**:
    - Der Standardwert ist 8.
    - Maximal zulässige Differenz (in Stunden) zwischen den Erstellungs Zeiten zweier verschiedener Blöcke, sodass Sie immer noch zusammengeführt werden können.
    - Timestamps sind die Werte für die Block Erstellung und stehen nicht in Beziehung zu den tatsächlichen Daten, die in den Blöcken enthalten sind.
    - Gilt für Merge-und Rebuild-Vorgänge.
    - Eine bewährte Vorgehensweise besteht darin, dass dieser Wert mit dem *Software Update*Punkt der [Beibehaltungs Richtlinie](./retentionpolicy.md)der Datenbank/Tabelle oder der *datahotspan* (der unteren der beiden) der [Cache Richtlinie](./cachepolicy.md)korreliert wird, sodass er zwischen 2-3% der letzteren liegt.

**`MaxRangeInHours`Beispiele**

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

Wenn eine Datenbank erstellt wird, wird Sie mit der standardmäßigen Zusammenstellungs Richtlinie (einer Richtlinie mit den oben erwähnten Standardwerten) festgelegt, die standardmäßig von allen in der Datenbank erstellten Tabellen geerbt wird (es sei denn, Ihre Richtlinien werden explizit auf Tabellenebene überschrieben).

Steuerungsbefehle, die das Verwalten von Merge-Richtlinien für Datenbanken/Tabellen erlauben, finden Sie [hier](../management/merge-policy.md).
