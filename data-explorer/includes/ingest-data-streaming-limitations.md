---
title: include file
description: include file
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: 60831cd9e4af4890a4afe788a42cbbd5cbb641aa
ms.sourcegitcommit: 35236fefb52978ce9a09bc36affd5321acb039a4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97531806"
---
## <a name="limitations"></a>Einschränkungen

* [Datenbankcursor](../kusto/management/databasecursor.md) werden für eine Datenbank nicht unterstützt, wenn für diese selbst oder für eine Ihrer Tabellen die [Richtlinie für die Streamingerfassung](../kusto/management/streamingingestionpolicy.md) definiert und aktiviert wurde.
* [Datenzuordnungen](../kusto/management/mappings.md) müssen [vorab erstellt](../kusto/management/create-ingestion-mapping-command.md) werden, damit sie bei der Streamingerfassung verwendet werden können. Individuelle Anforderungen zur Streamingerfassung bieten keine Inlinedatenzuordnungen.
* Leistung und Kapazität der Streamingerfassung werden für größere virtuelle Computer und Cluster skaliert. Die Anzahl der gleichzeitigen Erfassungsanforderungen ist auf sechs pro Kern beschränkt. Beispielsweise besteht die maximale unterstützte Last bei SKUs mit 16 Kernen (z. B. D14 und L16) aus 96 gleichzeitigen Erfassungsanforderungen. Bei SKUs mit 2 Kernen (z. B. D11) werden als maximale Last 12 gleichzeitige Erfassungsanfragen unterstützt.
* Die Datengröße für Anforderungen zur Streamingerfassung ist auf 4 MB beschränkt.
* Schemaaktualisierungen wie etwa die Erstellung und Änderung von Tabellen und Erfassungszuordnungen können für den Streamingerfassungsdienst bis zu fünf Minuten dauern. Weitere Informationen finden Sie unter [Streamingerfassung und Schemaänderungen](../kusto/management/data-ingestion/streaming-ingestion-schema-changes.md).
* Wenn die Streamingerfassung in einem Cluster aktiviert wird, wird ein Teil des lokalen SSD-Datenträgers der Clustercomputer für Streamingerfassungsdaten genutzt, wodurch sich der verfügbare Speicherplatz für den aktiven Cache verringert. Dies gilt auch, wenn gar keine Daten per Streaming erfasst werden.
* [Erweiterungstags](../kusto/management/extents-overview.md#extent-tagging) können für die Streamingerfassungsdaten nicht festgelegt werden.
* [Aktualisierungsrichtlinie:](../kusto/management/updatepolicy.md) Die Aktualisierungsrichtlinie kann nur auf die neu erfassten Daten in der Quelltabelle und nicht auf andere Daten oder Tabellen in der Datenbank verweisen.
* Wenn die Streamingerfassung für eine der Tabellen der Datenbank verwendet wird, kann diese Datenbank nicht als Leader für [Follower-Datenbanken](../follower.md) oder als [Datenanbieter](../data-share.md#data-provider---share-data) für Azure Data Share verwendet werden.
