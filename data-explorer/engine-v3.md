---
title: 'Azure Data Explorer: Kusto-EngineV3 (Vorschauversion)'
description: Enthält Informationen zur Kusto-EngineV3 von Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: e018e8ae6b25437a8665a5b5eb90fc2bac4ce9b9
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003271"
---
# <a name="enginev3---preview"></a>EngineV3 (Vorschauversion)

Die Kusto-EngineV3 ist die Speicher- und Abfrage-Engine der nächsten Generation von Azure Data Explorer. Sie bietet eine unvergleichlich hohe Leistung beim Erfassen und Abfragen von Telemetrie-, Protokoll- und Zeitreihendaten.

EngineV3 verfügt über ein neues optimiertes Speicherformat und entsprechende Indizes. Für EngineV3 wird eine Abfrageoptimierung mit erweiterten Datenstatistiken genutzt, um einen optimalen Abfrageplan zu erstellen und eine Abfrageausführung mit Just-In-Time-Kompilierung zu erzielen. Darüber hinaus verfügt EngineV3 auch über einen verbesserten Datenträgercache, der zu einer Abfrageleistung führt, die deutlich über der Leistung der aktuellen Engine (EngineV2) liegt. EngineV3 ist die Grundlage für die nächste Innovationswelle in Bezug auf den Azure Data Explorer-Dienst.

Da die Ausführung von Azure Data Explorer im EngineV3-Modus vollständig kompatibel mit EngineV2 ist, muss keine Datenmigration durchgeführt werden.

> [!IMPORTANT]
> Für die Verfügbarkeit von EngineV3 gelten die folgenden Phasen:
>
> 1. Public Preview (aktueller Status): Benutzer können neue Cluster im EngineV3-Modus erstellen. Während der öffentlichen Vorschauphase (Public Preview) unterliegen die Cluster keiner SLA, und für Azure Data Explorer-Markup werden keine Kosten berechnet. Die Infrastrukturkosten werden wie üblich berechnet.
> 1. Allgemeine Verfügbarkeit (General Availability, GA): Alle neuen Cluster werden standardmäßig im EngineV3-Modus erstellt. Die SLA gilt für alle EngineV3- und EngineV2-Produktionscluster.
> 1. Nach der Phase der allgemeinen Verfügbarkeit: Vorhandene Workloads, die im EngineV2-Modus ausgeführt werden, werden zu EngineV3 migriert. Die Kosten für Azure Data Explorer-Markup werden nun wieder berechnet.

## <a name="how-enginev3-works"></a>Funktionsweise von EngineV3

EngineV3 ist eine zusätzliche Columnstore-Speicher-Engine, die parallel zum vorhandenen Columnstore (EngineV2) und Rowstore (für Streamingerfassung) ausgeführt wird. Die Tabellen können gleichzeitig Daten aus allen drei Stores enthalten, und dieser „Verbund“ der Daten ist aus Sicht des Benutzers transparent.

:::image type="content" source="media\engine-v3\engine-v3-architecture.png" alt-text="Schematische Darstellung der Architektur von Azure Data Explorer/Kusto-EngineV3":::

Alle in Tabellen erfassten Daten werden in Shards partitioniert, bei denen es sich um horizontale Slices der Tabelle handelt. Jeder Shard enthält in der Regel einige Millionen Datensätze und wird unabhängig von anderen Shards codiert und indiziert. Diese Funktion ermöglicht es der Engine, eine lineare Skalierung des Erfassungsdurchsatzes zu erzielen.

Shards sind gleichmäßig auf die Clusterknoten verteilt und werden dort sowohl auf dem lokalen SSD-Datenträger als auch im Arbeitsspeicher zwischengespeichert. Über den Abfrageplaner und die Abfrage-Engine wird eine parallele Abfrage mit hohem Verteilungsgrad vorbereitet und ausgeführt, die von dieser Shardverteilung und -zwischenspeicherung profitiert.

Bei EngineV3 geht es hauptsächlich um die Optimierung dieses „unteren Teils“ der verteilten Abfrage.

## <a name="performance"></a>Leistung

Die verbesserte Leistung und höhere Geschwindigkeit von Abfragen beruht auf den zwei wichtigsten Änderungen der Engine:

* Neues und verbessertes Shardspeicherformat
* Umgestaltung der grundlegenden Abfrage-Engine für Shards

Das Ausmaß der Leistungssteigerung von EngineV3 hängt vom Dataset, den Abfragemustern, der Parallelität und den verwendeten VM-SKUs ab. Bei Leistungstests wurde ein 100-TB-Dataset verwendet, und es wurden unterschiedliche Szenarien untersucht, bei denen Analysen mit strukturierten, unstrukturierten und teilweise strukturierten Daten durchgeführt wurden. Bei Verwendung des gleichen Parallelitätsgrads und der gleichen Hardwarekonfiguration wurde im Durchschnitt eine Verbesserung der Leistung um das Achtfache erzielt. Die tatsächliche Leistungsverbesserung variiert je nach Abfrage und Dataset.

## <a name="create-an-enginev3-cluster"></a>Erstellen eines EngineV3-Clusters

Aktivieren Sie zum [Erstellen eines neuen Clusters](create-cluster-database-portal.md) mit EngineV3 auf der Registerkarte **Grundlagen** des Bildschirms für die Clustererstellung das Kontrollkästchen **Use Engine V3 preview** (EngineV3-Vorschauversion verwenden):

:::image type="content" source="media/engine-v3/create-new-cluster-v3.png" alt-text="Schematische Darstellung der Architektur von Azure Data Explorer/Kusto-EngineV3":::

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über die Datenerfassung in Azure Data Explorer](ingest-data-overview.md)
