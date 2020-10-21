---
title: 'Daten Sharding-Richtlinie: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Daten Sharding-Richtlinie in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 70d4013c8524fa88249de14a0a67cc8a85e73b3f
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337566"
---
# <a name="data-sharding-policy"></a>Daten Sharding-Richtlinie

Die Sharding-Richtlinie definiert, ob und wie [Blöcke (Data Shards)](../management/extents-overview.md) im Azure-Daten-Explorer Cluster versiegelt sein sollten.

> [!NOTE]
> Die Richtlinie gilt für alle Vorgänge, mit denen neue Blöcke erstellt werden, z. b. Befehle für die [Daten](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands)Erfassung und [Merge-und Rebuild-Befehle](./merge-extents.md) .

Die Daten Sharding-Richtlinie enthält die folgenden Eigenschaften:

- **Maxrowcount**:
    - Maximale Zeilen Anzahl für einen durch einen Erfassungs-oder Neuerstellungs Vorgang erstellten Wertebereich.
    - Der Standardwert ist 750.000.
    - Für [Merge-Vorgänge](mergepolicy.md) **nicht wirksam** .
        - Wenn Sie die Anzahl der Zeilen in Blöcken begrenzen müssen, die von Mergevorgängen erstellt werden, passen Sie die `RowCountUpperBoundForMerge` -Eigenschaft in der [Blöcke-Zusammenstellungs Richtlinie](mergepolicy.md)der Entität an.
- **Maxextentsizzumb**:
    - Die maximal zulässige komprimierte Datengröße (in Megabyte) für einen von einem Zusammenarbeits Bereich erstellten Wertebereich.
    - **Nur für [Merge](mergepolicy.md) -Vorgänge**.
    - Der Standardwert ist 1.024 (1 GB).

- **Maxoriginalsizzumb**:
    - Die maximal zulässige ursprüngliche Datengröße (in Megabyte) für einen durch einen Neuerstellungs Vorgang erstellten Wertebereich.
    - **Nur für [rebuild](mergepolicy.md) **Neuerstellung-Vorgänge.
    - Der Standardwert ist 2.048 (2 GB).

> [!WARNING]
> Wenden Sie sich vor dem Ändern einer Daten Sharding-Richtlinie an das Azure Daten-Explorer-Team.

Wenn eine Datenbank erstellt wird, enthält Sie die standardmäßige Daten Sharding-Richtlinie. Diese Richtlinie wird von allen Tabellen geerbt, die in der Datenbank erstellt wurden (es sei denn, die Richtlinie wird explizit auf Tabellenebene überschrieben).

Verwenden Sie die [Befehle der Sharding-Richtlinien Steuerung](../management/sharding-policy.md)), um Daten Sharding-Richtlinien für Datenbanken und Tabellen zu verwalten.
