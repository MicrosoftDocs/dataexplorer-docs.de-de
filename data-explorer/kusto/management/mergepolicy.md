---
title: Extents Merge-Richtlinie - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Zusammenführungsrichtlinie für Erweiterungen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 771af22f07a770b0da1196871e9132393524aab9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520722"
---
# <a name="extents-merge-policy"></a>Extents Merge-Richtlinie
Die Mergerichtlinie definiert, ob und wie [Extents (Data Shards)](../management/extents-overview.md) im Kusto-Cluster zusammengeführt werden sollen.

Es gibt 2 Varianten für `Merge` Merge-Operationen: (die `Rebuild` Indizes neu erstellt) und (die vollständig die Daten neu aufnimmt).

Beide Vorgangsarten ergeben eine einzige Ausdehnung, die die Quellausdehnungen ersetzt.

Standardmäßig werden Neuaufbauvorgänge bevorzugt, und nur wenn noch Ausdehnungen vorhanden sind, die nicht den Kriterien für die Neuerstellung entsprechen, werden sie zusammengeführt.  

*Hinweise:*
- Das Markieren von Ausdehnungen mit *unterschiedlichen* `drop-by` Tags führt dazu, dass solche Ausdehnungen nicht zusammengeführt werden, selbst wenn eine Mergerichtlinie festgelegt wurde (siehe [Ausdehnungsmarkierung](../management/extents-overview.md#extent-tagging)).
- Ausdehnungen, deren Vereinigung von Tags die Länge von 1M-Zeichen überschreitet, werden nicht zusammengeführt.
- Die [Sharding-Richtlinie](./shardingpolicy.md) der Datenbank /Tabelle hat auch einen gewissen Einfluss darauf, wie Ausdehnungen zusammengeführt werden.

Die Mergerichtlinie enthält die folgenden Eigenschaften:

- **RowCountUpperboundForMerge**:
    - Der Standardwert ist 0.
    - Maximal zulässige Zeilenanzahl der zusammengeführten Ausdehnung.
    - Gilt für Zusammenführungsvorgänge, nicht für Rebuild.  
- **MaxExtentsToMerge**:
    - Standardwert 100.
    - Maximal zulässige Anzahl von Ausdehnungen, die in einem einzigen Vorgang zusammengeführt werden sollen.
    - Gilt für Mergevorgänge.
- **LoopPeriod**:
    - Standardwerte für 01:00:00 (1 Stunde).
    - Die maximale Wartezeit zwischen dem Starten von zwei aufeinander folgenden Iterationen von Merge-/Neuerstellungsvorgängen (vom Datenverwaltungsdienst ausgeführt).
    - Gilt sowohl für Merge- als auch für Rebuild-Vorgänge.
- **AllowRebuild**:
    - Standardmäßig ist "true".
    - Definiert, `Rebuild` ob Vorgänge aktiviert sind (in diesem `Merge` Fall werden sie Vorgängen vorgezogen).
- **AllowMerge**:
    - Standardmäßig ist "true".
    - Definiert, `Merge` ob Vorgänge aktiviert sind (in diesem `Rebuild` Fall werden sie weniger bevorzugt als Vorgänge).
- **MaxRangeInHours**:
    - Standardwert 8.
    - Maximal zulässige Differenz (in Stunden) zwischen den Erstellungszeiten von 2 verschiedenen Ausdehnungen, sodass sie weiterhin zusammengeführt werden können.
    - Zeitstempel sind solche der Erweiterungserstellung und beziehen sich nicht auf die tatsächlichen Daten, die in den Ausdehnungen enthalten sind.
    - Gilt sowohl für Merge- als auch für Rebuild-Vorgänge.
    - Es wird jedoch bestpractice, dass dieser Wert mit der [Retentionsrichtlinie](./retentionpolicy.md)der Datenbank/Tabelle *SoftDeletePeriod*oder der [Cache-Richtlinie](./cachepolicy.md)'s *DataHotSpan* (der untere der beiden) korreliert, sodass er zwischen 2-3% der letzten Werte liegt.

**`MaxRangeInHours`Beispiele:**
|min(SoftDeletePeriod (Aufbewahrungsrichtlinie), DataHotSpan (Cache-Richtlinie))|Max. Reichweite in Stunden (Merge-Richtlinie)
|---|---
|7 Tage (168 Stunden)| 4
|14 Tage (336 Stunden)| 8
|30 Tage (720 Stunden)| 18
|60 Tage (1.440 Stunden)| 36
|90 Tage (2.160 Stunden)| 60
|180 Tage (4.320 Stunden)| 120
|365 Tage (8.760 Stunden)| 250

> [!WARNING]
> Es wird selten empfohlen, eine Extents Merge Policy ohne Rücksprache mit dem Kusto-Team zu ändern.

Wenn eine Datenbank erstellt wird, wird sie mit der Standard-Merge-Richtlinie (einer Richtlinie mit den oben genannten Standardwerten) festgelegt, die standardmäßig von allen in der Datenbank erstellten Tabellen geerbt wird (es sei denn, ihre Richtlinien werden explizit auf Tabellenebene überschrieben).

Steuerbefehle, die die Verwaltung von Mergerichtlinien für Datenbanken / Tabellen ermöglichen, finden Sie [hier](../management/merge-policy.md).