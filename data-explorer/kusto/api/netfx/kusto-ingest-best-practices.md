---
title: Kusto-Ingest-Clientbibliothek - Bewährte Methoden - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Kusto Ingest Client Library – Best Practices in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/16/2019
ms.openlocfilehash: e28ec3f99abe4163b83721d753da98c0035d1e46
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523697"
---
# <a name="kusto-ingest-client-library---best-practices"></a>Kusto Ingest Client Library – Bewährte Methoden

## <a name="choosing-the-right-ingestclient-flavor"></a>Die Wahl des richtigen IngestClient-Geschmacks
Die Verwendung von [KustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) ist der empfohlene systemeigene Datenerfassungsmodus. Dies ist der Grund:
* Die direkte Erfassung ist während der Ausfallzeit des Kusto-Moduls (z. B. während der Bereitstellung) nicht möglich, während im Warteschlangenerfassungsmodus die Anforderungen in der Azure-Warteschlange beibehalten werden und der Datenverwaltungsdienst bei Bedarf erneut versucht.
* Der Datenverwaltungsdienst ist dafür verantwortlich, das Modul nicht mit Aufnahmeanforderungen zu überlasten. Das Überschreiben dieses Steuerelements (z. B. die direkte Einnahme) kann die Motorleistung erheblich beeinträchtigen, sowohl die Aufnahme als auch die Abfrage.
* Data Management aggregiert mehrere Erfassungsanforderungen, um die Größe des zu erstellenden ursprünglichen Shards (Ausdehnung) zu optimieren.
* Es gibt eine bequeme Möglichkeit, Feedback über jede Einnahme zu erhalten - ob erfolgreich oder nicht.

## <a name="tracking-ingest-operation-status"></a>Nachverfolgen des Status des Aufnahmevorgangs
[Das Nachverfolgen des Status des Aufnahmevorgangs](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient) ist ein nützliches Feature in Kusto, jedoch kann das Einschalten für die Erfolgsberichterstattung leicht in dem Maße missbraucht werden, in dem sie Ihren Dienst lahmlegen wird.<BR>

> [!WARNING]
> Das Aktivieren positiver Benachrichtigungen für jede Aufnahmeanforderung für Datenströme mit großem Volumen sollte vermieden werden, da dies die zugrunde liegenden xStore-Ressourcen extrem belastet, > was zu einer erhöhten Aufnahmelatenz und sogar zur vollständigen Cluster-Nichtreaktion führen kann.

## <a name="optimizing-for-throughput"></a>Optimierung für Durchsatz
* Die Aufnahme funktioniert am besten (d. h., sie verbraucht die geringsten Ressourcen während der Aufnahme, erzeugt die meisten COGS-optimierten Datenshards und führt zu den leistungsstärksten Datenartefakten), wenn sie in großen Blöcken ausgeführt wird. Im Allgemeinen empfehlen wir Kunden, die Daten mit der Kusto.Ingest-Bibliothek oder direkt in das Modul aufnehmen, Daten in Batches von **100 MB bis 1 GB (unkomprimiert)** zu senden.
* Die obere Grenze ist wichtig, wenn Sie direkt mit dem Kusto-Modul arbeiten, um `KustoQueuedIngestClient` die Speichermenge zu reduzieren, die vom Aufnahmeprozess verwendet wird (bei Verwendung der Klasse werden zu große Datenblöcke auf dem Client in kleinere Teile aufgeteilt, und kleine Blöcke werden bis zu einem gewissen Grad aggregiert, bevor die Kusto Engine erreicht wird).
* Die untere Grenze für die aufgenommene Datengröße ist ebenfalls wichtig, wenn auch weniger kritisch. Die Einfallsmenge von Daten in kleinen Chargen ist hin und wieder vollkommen in Ordnung, wenn auch etwas weniger effizient als bei großen Chargen. `KustoQueuedIngestClient`Class löst auch das Problem für Kunden, die große Datenmengen aufnehmen müssen und diese nicht in große Blöcke stapeln können, bevor sie an Kusto gesendet werden

## <a name="factors-impacting-ingestion-throughput"></a>Faktoren, die den Aufnahmedurchsatz beeinflussen
Mehrere Faktoren können sich auf den Durchsatz der Aufnahme auswirken. Achten Sie bei der Planung Ihrer Kusto-Aufnahmepipeline darauf, die folgenden Punkte zu bewerten, was erhebliche Auswirkungen auf Ihre COGs haben kann.
* Datenformat - CSV ist das schnellste Format, das zu erfassen ist, JSON dauert in der Regel x2 oder x3 länger für das gleiche Datenvolumen
* Tabellenbreite - Stellen Sie sicher, dass Sie nur Daten aufnehmen, die Sie wirklich benötigen, da je breiter die Tabelle, desto mehr Spalten werden codiert und indiziert, daher je niedriger der Durchsatz.
    Sie können steuern, welche Felder aufgenommen werden, indem Sie eine Aufnahmezuordnung bereitstellen.
* Quelldatenspeicherort - Vermeidung von regionsübergreifenden Lesevorgängen beschleunigt die Aufnahme
* Laden auf den Cluster : Wenn clusterlange Abfragelasten zu sich nehmen, dauert die Erfassung länger, wodurch der Durchsatz reduziert wird.
* Aufnahmemuster - Die Aufnahme ist optimal, wenn der Cluster mit Batches von bis zu `KustoQueuedIngestClient`1 GB serviert wird (mit Vorsicht bei der Verwendung von )

## <a name="optimizing-for-cogs"></a>Optimierung für COGS
Während die Verwendung von Kusto-Clientbibliotheken, um Daten in Kusto aufzunehmen, die billigste und robusteste Option bleibt, fordern wir unsere Kunden auf, ihre Aufnahmetaktik zu überprüfen und die jüngsten (Herbst 2017) Änderungen der Azure Storage-Preise zu berücksichtigen, die Blobtransaktionen erheblich verteuern (x100).
<BR>
Es ist wichtig zu verstehen, dass je mehr Datenblöcke (Dateien/Blobs/Streams) Sie an Kusto senden, desto größer wird Ihre monatliche Rechnung.
Wenn Sie die folgenden Empfehlungen befolgen, können Sie Ihre Kusto-Aufnahmekosten besser kontrollieren:
* **Bevorzugen Sie die Aufnahme größerer Datenblöcke (bis zu 1 GB unkomprimierter Daten)**<br>
    Viele Teams versuchen, eine niedrige Latenz zu erreichen, indem sie zig Millionen winzigedatenteile einfließen, was extrem ineffizient und sehr kostspielig ist.<br>
    Jede Batchverarbeitung auf der Clientseite würde helfen. 
* **Stellen Sie sicher, dass Sie dem Kusto.Ingest-Client eine genaue unkomprimierte Datengröße zur Verfügung stellen.**<br>
    Wenn dies nicht der Fall ist, kann Kusto dazu führen, dass zusätzliche Speichertransaktionen ausgeführt werden.
* **Vermeiden Sie** das Senden von `FlushImmediately` Daten `true`für die Aufnahme mit `ingest-by` / `drop-by` dem flag auf , oder das Senden kleiner Blöcke mit Tags gesetzt.<br>
    Die Verwendung dieser Daten verhindert, dass der Kusto-Dienst die Daten während der Aufnahme ordnungsgemäß aggregiert, und führt zu unnötigen Speichertransaktionen nach der Aufnahme, die sich auf COGS auswirken.<br>
    Darüber hinaus kann die übermäßige Verwendung dieser Daten zu einer beeinträchtigten Aufnahme- und/oder Abfrageleistung in Ihrem Cluster führen.<br>
    