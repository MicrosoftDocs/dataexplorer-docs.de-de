---
title: Bewährte Methoden für die Erfassung von Client Bibliotheken in Kusto-Azure Daten-Explorer
description: In diesem Artikel werden bewährte Methoden für die Kusto-Erfassungs Client Bibliothek beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/16/2019
ms.openlocfilehash: 1e7cbbb62c58b83690dd8eb272395aacffd3eda2
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373747"
---
# <a name="kusto-ingest-client-library---best-practices"></a>Kusto-Erfassungs Client Bibliothek-bewährte Methoden

## <a name="select-the-right-ingestclient-flavor"></a>Wählen Sie die richtige ingestclient-Konfiguration aus.

Verwenden Sie [kustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient), den empfohlenen systemeigenen Daten Erfassungs Modus. Dies ist der Grund:
* Die direkte Erfassung ist während der Engine-Ausfallzeit, wie während der Bereitstellung, nicht möglich. Im Erfassungs Modus für die Warteschlange werden die Anforderungen in der Azure-Warteschlange persistent gespeichert, und der Datenverwaltung-Dienst wird bei Bedarf wiederholt.
* Der Datenverwaltung-Dienst sorgt dafür, dass die Engine nicht mit Erfassungs Anforderungen überladen wird. Wenn Sie dieses Steuerelement beispielsweise mithilfe der direkten Erfassung überschreiben, kann dies die Engine-Erfassung und-Abfrageleistung stark beeinträchtigen.
* Datenverwaltung aggregiert mehrere Anforderungen für die Erfassung. Die Aggregation optimiert die Größe des anfänglichen Shards (Block), der erstellt werden soll.
* Es ist einfach, Feedback zu den einzelnen Erfassungs Informationen zu erhalten.

## <a name="avoid-tracking-ingest-operation-status"></a>Vermeiden des Erfassungs Vorgangs Status

Der [Status der Erfassung des Erfassungs Vorgangs](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient) ist hilfreich. Bei umfangreichen Datenströmen sollte das Aktivieren positiver Benachrichtigungen für jede Erfassungs Anforderung jedoch vermieden werden. Eine solche Nachverfolgung bringt eine extrem große Belastung für die zugrunde liegenden XStore-Ressourcen mit sich, die zu erhöhter Erfassungs Latenz und sogar zu einer nicht Reaktionsfähigkeit des Clusters führen können.

## <a name="optimizing-for-throughput"></a>Optimieren des Durchsatzes

Die Erfassung funktioniert am besten, wenn Sie in großen Blöcken ausgeführt wird. 
* Dabei werden die geringsten Ressourcen beansprucht.
* Sie erzeugt die meisten COGS (Kosten der verkauften Waren), optimierte Daten-Shards und führt zu den besten Daten Transaktionen.

Wir empfehlen Kunden, die Daten mit der Kusto. Erfassungs Bibliothek oder direkt in der Engine erfassen, um Daten in Batches von **100 MB** an **1 GB (unkomprimiert)** zu senden.
* Die Obergrenze ist wichtig, wenn Sie direkt mit der Engine arbeiten, um die Menge des vom Erfassungsprozess genutzten Arbeitsspeichers zu reduzieren. 

> [!NOTE]
> Wenn Sie die- `KustoQueuedIngestClient` Klasse verwenden, werden übermäßig große Datenblöcke in kleinere Blöcke aufgeteilt, und diese kleinen Blöcke werden zu einem bestimmten Zeitpunkt aggregiert, bevor die Engine erreicht wird.

* Der untere Grenzwert für die erfasste Datengröße ist ebenfalls wichtig, obwohl weniger kritisch ist. Das Erfassen von Daten in kleinen Batches wird alle angezeigt und ist dann problemlos einwandfrei, obwohl etwas weniger effizient als die Verwendung großer Batches ist. Die `KustoQueuedIngestClient` -Klasse löst auch das Problem für Kunden, die große Datenmengen erfassen müssen, und kann Sie nicht in große Blöcke Stapeln, bevor Sie an die-Engine gesendet werden.

## <a name="other-factors-that-impact-ingestion-throughput"></a>Weitere Faktoren, die sich auf den Erfassungs Durchsatz auswirken

Mehrere Faktoren können sich auf den Erfassungs Durchsatz auswirken. Stellen Sie beim Planen der Erfassungs Pipeline sicher, dass Sie die folgenden Punkte evaluieren, die sich auf Ihre COGS auswirken können.

| Faktor für die Überlegung |  Beschreibung                                                                                              |
|--------------------------|-----------------------------------------------------------------------------------------------------------|
| Datenformat              | CSV ist das schnellste Format für die Erfassung. JSON dauert in der Regel 2X oder 3X länger für dieselbe Datenmenge.|
| Tabellenbreite              | Stellen Sie sicher, dass nur Daten erfasst werden, die Sie wirklich benötigen. Je größer die Tabelle ist, desto mehr Spalten müssen codiert und indiziert werden, und desto geringer der Durchsatz. Sie können steuern, welche Felder erfasst werden, indem Sie eine Erfassungs Zuordnung bereitstellen.       |
| Quelldaten Speicherort     | Vermeiden Sie Regions übergreifende Lesevorgänge, um die Erfassung zu beschleunigen.                                                       |
| Laden des Clusters      | Wenn ein Cluster eine hohe Abfrage Auslastung hat, dauert die Ausführung von Ingestionen länger, was den Durchsatz reduziert.|

## <a name="optimizing-for-cogs"></a>Optimieren für COGS

Die Verwendung von Kusto-Client Bibliotheken zum Erfassen von Daten in Azure Daten-Explorer ist die günstigste und stabilste Option. Wir empfehlen unseren Kunden, ihre Erfassungsmethoden zur Optimierung von COGS (Kosten für verkaufte waren) zu überprüfen und die Azure Storage Preise zu nutzen, mit denen BLOB-Transaktionen erheblich kostengünstig werden.

* **Begrenzen Sie die Anzahl der** erfassten Datenblöcke.
    Beschränken Sie die Anzahl der erfassten Datenblöcke (Dateien/blobdateien/Streams), um eine bessere Kontrolle über ihre Kosten bei der Azure-Daten-Explorer Erfassung zu erhalten und Ihre monatliche Rechnung zu verringern.
* Erfassen **großer Datenblöcke (bis zu 1 GB unkomprimierter Daten)**. 
    Viele Teams versuchen, eine geringe Latenz zu erzielen, indem Sie zehn Millionen von kleinen Datenblöcken erfassen, was ineffizient und kostspielig ist. 
* **Batch** Verarbeitung. Jede Menge an Batch Verarbeitung auf Clientseite würde die Optimierung verbessern. 
* **Stellen Sie den Kusto. Erfassungs Client mit einer exakten, unkomprimierten Datengröße bereit**.
    Dies führt möglicherweise zu zusätzlichen Speicher Transaktionen.
* **Vermeiden** Sie das Senden von Daten für die Erfassung, wobei das `FlushImmediately` Flag auf festgelegt ist `true` . Vermeiden Sie außerdem das Senden kleiner Blöcke mit `ingest-by` / `drop-by` festgelegten Tags. Wenn Sie diese Methoden verwenden, gehen Sie wie folgt vor:
     * verhindern, dass der Dienst die Daten während der Erfassung ordnungsgemäß aggregierte
     * unnötige Speicher Transaktionen nach der Erfassung verursachen
     * Auswirkungen auf COGS 
     * Dies führt wahrscheinlich zu einer beeinträchtigten Erfassungs-oder Abfrageleistung Ihres Clusters, wenn übermäßig häufig verwendet wird.
