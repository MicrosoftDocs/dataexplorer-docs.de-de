---
title: Kusto-Erfassungs Client Bibliothek-bewährte Methoden-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden die bewährten Methoden der Kusto-Erfassungs Client Bibliothek in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/16/2019
ms.openlocfilehash: 2369d352e316f1d9b49de71fb197507280598754
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617934"
---
# <a name="kusto-ingest-client-library---best-practices"></a>Kusto-Erfassungs Client Bibliothek-bewährte Methoden

## <a name="choosing-the-right-ingestclient-flavor"></a>Auswählen der richtigen ingestclient-Konfiguration

Die Verwendung von [kustoqueuedingestclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) ist der empfohlene systemeigene Daten Erfassungs Modus. Dies ist der Grund:
* Die direkte Erfassung ist während der Engine-Ausfallzeit (z. b. während der Bereitstellung) nicht möglich, während die Anforderungen im Erfassungs Modus der Warteschlange dauerhaft in der Azure-Warteschlange gespeichert werden und der Datenverwaltung-Dienst bei Bedarf wiederholt wird.
* Der Datenverwaltung-Dienst ist dafür verantwortlich, die Engine nicht mit Erfassungs Anforderungen zu überladen. Das Überschreiben dieses Steuer Elements (z. b. die direkte Erfassung) beeinträchtigt möglicherweise die Engine-Leistung, sowohl die Erfassung als auch die Abfrage.
* Datenverwaltung aggregiert mehrere Erfassungs Anforderungen, um die Größe des anfänglichen Shards (Block) zu optimieren, der erstellt werden soll.
* Es ist einfach, Feedback zu den einzelnen Erfassungs Möglichkeiten zu erhalten.

## <a name="tracking-ingest-operation-status"></a>Status der Erfassung des Erfassungs Vorgangs

Der [Status der Erfassung des Erfassungs Vorgangs](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient) ist ein nützliches Feature. Allerdings kann es leicht missbraucht werden, um die erfolgreiche Berichterstellung zu aktivieren.

> [!WARNING]
> Das Aktivieren positiver Benachrichtigungen für jede Erfassungs Anforderung für große volumedatenströme sollte vermieden werden, da dadurch die zugrunde liegenden XStore-Ressourcen extrem belastet werden. Die zusätzliche Auslastung kann zu einer längeren Erfassungs Latenz und sogar zu einem kompletten Cluster ohne Reaktionsfähigkeit führen.

## <a name="optimizing-for-throughput"></a>Optimieren des Durchsatzes

* Die Erfassung funktioniert am besten, wenn Sie in großen Blöcken ausgeführt wird. Dabei werden die geringsten Ressourcen beansprucht, die meisten COGS-optimierten Daten-Shards erzeugt und die am besten leistungsfähigen Daten Artefakte erzielt. Im Allgemeinen empfehlen wir Kunden, die Daten mit der Kusto. Erfassungs Bibliothek oder direkt in der Engine erfassen, um Daten in Batches von **100 MB an 1 GB (unkomprimiert) zu** senden.
* Die Obergrenze ist wichtig, wenn Sie direkt mit der Engine arbeiten, um die Menge des vom Erfassungsprozess genutzten Arbeitsspeichers zu reduzieren. 

> [!NOTE]
> Wenn Sie die `KustoQueuedIngestClient` -Klasse verwenden, werden übermäßig große Datenblöcke in kleinere Blöcke aufgeteilt, und diese kleinen Blöcke werden zu einem bestimmten Zeitpunkt aggregiert, bevor die Engine erreicht wird.

* Der untere Grenzwert für die erfasste Datengröße ist ebenfalls wichtig, obwohl weniger kritisch ist. Das Erfassen von Daten in kleinen Batches wird alle angezeigt und ist dann problemlos einwandfrei, obwohl etwas weniger effizient als die Verwendung großer Batches ist. Die `KustoQueuedIngestClient` -Klasse löst auch das Problem für Kunden, die große Datenmengen erfassen müssen, und kann Sie nicht in große Blöcke Stapeln, bevor Sie an die-Engine gesendet werden.

## <a name="factors-impacting-ingestion-throughput"></a>Faktoren, die sich auf den Erfassungs Durchsatz auswirken

Mehrere Faktoren können sich auf den Erfassungs Durchsatz auswirken. Stellen Sie beim Planen der Erfassungs Pipeline sicher, dass Sie die folgenden Punkte evaluieren, die sich auf Ihre COGS auswirken können.

| Faktor für die Überlegung |  Decription                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------|
| Datenformat              | CSV ist das schnellste zu erfasse Format, JSON dauert in der Regel 2X oder 3X länger für dieselbe Datenmenge.|
| Tabellenbreite              | Stellen Sie sicher, dass nur Daten erfasst werden, die Sie wirklich benötigen. Je größer die Tabelle ist, desto mehr Spalten müssen codiert und indiziert werden. Daher wird der Durchsatz gesenkt. Sie können steuern, welche Felder erfasst werden, indem Sie eine Erfassungs Zuordnung bereitstellen.|
| Quelldaten Speicherort     | Vermeiden Sie Regions übergreifende Lesevorgänge, um die Erfassung zu beschleunigen.                                                       |
| Laden des Clusters      | Wenn ein Cluster eine hohe Abfrage Auslastung hat, dauert die Ausführung von Ingestionen länger, was den Durchsatz reduziert.|
| Erfassungs Muster        | Die Erfassung erfolgt am optimalen, wenn der Cluster mit Batches von bis zu 1 GB (verarbeitet durch `KustoQueuedIngestClient`) bedient wird. |

## <a name="optimizing-for-cogs"></a>Optimieren für COGS

Die Verwendung von Kusto-Client Bibliotheken zum Erfassen von Daten in Kusto bleibt die billigste und stabilste Option. Wir empfehlen unseren Kunden, ihre Erfassungsmethoden zu überprüfen und die Azure Storage Preise zu nutzen, mit denen BLOB-Transaktionen erheblich kostengünstig werden.

Beschränken Sie die Anzahl der erfassten Datenblöcke (Dateien/blobdateien/Streams), um eine bessere Kontrolle über ihre Kosten bei der Azure-Daten-Explorer Erfassung zu erhalten und Ihre monatliche Rechnung zu verringern.

* **Ziehen Sie die Erfassung größerer Datenblöcke (bis zu 1 GB unkomprimierter Daten)** vor. 
    Viele Teams versuchen, eine geringe Latenz zu erzielen, indem Sie zehn Millionen von kleinen Datenblöcken erfassen, was extrem ineffizient und sehr kostspielig ist. Eine beliebige Menge an Batch Verarbeitung auf Clientseite würde dabei helfen. 
* Stellen **Sie sicher, dass Sie den Kusto. Erfassungs Client mit einer exakten, unkomprimierten Datengröße bereitstellen**.
    Dies führt möglicherweise zu zusätzlichen Speicher Transaktionen.
* **Vermeiden** Sie das Senden von Daten für die `FlushImmediately` Erfassung, wenn `true`das Flag auf festgelegt ist oder `ingest-by` / `drop-by` kleine Blöcke mit festgelegtem Tag gesendet werden.
    Durch die Verwendung dieser Methoden wird verhindert, dass der Dienst die Daten während der Erfassung ordnungsgemäß aggregierte, und es werden nach der Erfassung unnötige Speicher Transaktionen verursacht, was sich auf die COGS auswirkt.
    
    Darüber hinaus kann die Verwendung dieser übermäßig zu einer beeinträchtigten Erfassungs-und/oder Abfrageleistung Ihres Clusters führen.
    
