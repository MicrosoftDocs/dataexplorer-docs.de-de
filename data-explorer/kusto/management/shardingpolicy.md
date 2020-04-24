---
title: Datenshardrichtlinie - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Datenshardrichtlinie in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 6ed9cf3a1667fb57271a44dcfe7c6dd5318c4010
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520025"
---
# <a name="data-sharding-policy"></a>Daten-Sharding-Richtlinie

Die Shardingrichtlinie definiert, ob und wie [Ausdehnungen (Datenshards)](../management/extents-overview.md) im Azure Data Explorer-Cluster versiegelt werden sollen.

> [!NOTE]
> Die Richtlinie gilt für alle Vorgänge, die neue Ausdehnungen erstellen, z. B. Befehle für die [Datenerfassung](../management/data-ingestion/index.md), [und .merge- und .rebuild-Befehle](../management/extents-commands.md#merge-extents)

Die Daten-Sharding-Richtlinie enthält die folgenden Eigenschaften:

- **MaxRowCount**:
    - Maximale Zeilenanzahl für eine Ausdehnung, die durch einen Aufnahme- oder Neuerstellungsvorgang erstellt wurde.
    - Standardwert: 750.000.
    - **Nicht in Kraft** für [Zusammenführungsvorgänge](mergepolicy.md).
        - Wenn Sie die Anzahl der Zeilen in Ausdehnungen `RowCountUpperBoundForMerge` begrenzen müssen, die von Zusammenführungsvorgängen erstellt wurden, passen Sie die Eigenschaft in der Mergerichtlinie der [Entitätserweiterungen](mergepolicy.md)an.
- **MaxExtentSizeInMb**:
    - Maximal zulässige komprimierte Datengröße (in Megabyte) für eine Ausdehnung, die durch einen Zusammenführungsvorgang erstellt wird.
    - In der Tat **gilt nur für [Zusammenführungsvorgänge](mergepolicy.md) **.
    - Standardwerte auf 1.024 (1 GB).

- **MaxOriginalSizeInMb**:
    - Maximal zulässige ursprüngliche Datengröße (in Megabyte) für eine Ausdehnung, die durch einen Neuerstellungsvorgang erstellt wurde.
    - In der Tat **nur für [Wiederherstellungsvorgänge](mergepolicy.md) **.
    - Standardwerte auf 2.048 (2 GB).

> [!WARNING]
> Wenden Sie sich an das Azure Data Explorer-Team, bevor Sie eine Datenshardrichtlinie ändern.

Wenn eine Datenbank erstellt wird, enthält sie die Standardrichtlinie für datensharding. Diese Richtlinie wird von allen Tabellen geerbt, die in der Datenbank erstellt wurden (es sei denn, die Richtlinie wird explizit auf Tabellenebene überschrieben).

Verwenden Sie die [Sharding-Richtliniensteuerungsbefehle](../management/sharding-policy.md)), um Datenshardrichtlinien für Datenbanken und Tabellen zu verwalten.
 