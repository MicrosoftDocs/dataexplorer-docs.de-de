---
title: 'Azure Data Explorer: Kusto-EngineV3 (Vorschauversion)'
description: Enthält Informationen zur Kusto-EngineV3 von Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: 133f01498d4cf430d7fdc2649df88186610b3954
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388969"
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

* **Neues und verbessertes Shardspeicherformat** Ähnlich wie bei EngineV2 wird als Speicherformat ein komprimierter Spaltenspeicher verwendet – mit besonderem Augenmerk auf unstrukturierte Datentypen (Text) und teilweise strukturierte Datentypen. EngineV3 verbessert die Codierung dieser verschiedenen Datentypen. Indizes wurden neu gestaltet, um die Granularität zu erhöhen, sodass Teile der Abfrage basierend auf dem Index ausgewertet werden können, ohne die Daten zu scannen.
* **Umgestaltung der grundlegenden Abfrage-Engine für Shards** Die neue Shardabfrage wird mit Just-In-Time-Kompilierung in hocheffizienten Computercode umgewandelt. Das Ergebnis ist eine schnelle und effiziente Abfrageauswertungslogik. Die Abfragekompilierung orientiert sich an den von allen Shards gesammelten Datenstatistiken und ist auf die Besonderheiten der Spaltencodierung zugeschnitten.

Das Ausmaß der Leistungssteigerung von EngineV3 hängt vom Dataset, den Abfragemustern, der Parallelität und den verwendeten VM-SKUs ab. Bei Leistungstests wurde ein 100-TB-Dataset verwendet, und es wurden unterschiedliche Szenarien untersucht, bei denen Analysen mit strukturierten, unstrukturierten und teilweise strukturierten Daten durchgeführt wurden. Bei Verwendung des gleichen Parallelitätsgrads und der gleichen Hardwarekonfiguration wurde im Durchschnitt eine Verbesserung der Leistung um das Achtfache erzielt. Die tatsächliche Leistungsverbesserung variiert je nach Abfrage und Dataset.

## <a name="create-an-enginev3-cluster"></a>Erstellen eines EngineV3-Clusters

Aktivieren Sie zum [Erstellen eines neuen Clusters](create-cluster-database-portal.md) mit EngineV3 auf der Registerkarte **Grundlagen** des Bildschirms für die Clustererstellung das Kontrollkästchen **Use Engine V3 preview** (EngineV3-Vorschauversion verwenden):

:::image type="content" source="media/engine-v3/create-new-cluster-v3.png" alt-text="Screenshot: Kontrollkästchen „Use Engine V3 preview“ (EngineV3-Vorschauversion verwenden) beim Erstellen eines Clusters":::

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über die Datenerfassung in Azure Data Explorer](ingest-data-overview.md)
