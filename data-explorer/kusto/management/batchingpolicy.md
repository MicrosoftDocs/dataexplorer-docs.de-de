---
title: 'Kusto ingestionbatching-Richtlinie optimiert die Batch Verarbeitung: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Richtlinie ingestionbatching in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 0a493c4e808a43d04714487ada17964ab048de6f
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373764"
---
# <a name="ingestionbatching-policy"></a>Ingestionbatching-Richtlinie

## <a name="overview"></a>Übersicht

Während des Erfassungs Prozesses versucht Kusto, den Durchsatz zu optimieren, indem kleine eingehende Datenblöcke zusammengefasst werden, während Sie auf die Erfassung warten.
Durch diese Art der Batch Verarbeitung werden die vom Erfassungsprozess genutzten Ressourcen reduziert, und es sind keine Ressourcen nach der Erfassung erforderlich, um die kleinen Daten-Shards zu optimieren, die durch die nicht-Batch Erfassung erzeugt werden.

Es gibt jedoch einen Nachteil, um die Batch Verarbeitung vor der Erfassung auszuführen. Dies ist die Einführung einer erzwungenen Verzögerung, sodass die End-to-End-Zeit von der Anforderung der Erfassung von Daten, bis Sie für die Abfrage bereit ist, größer ist.

Um die Kontrolle über diesen Kompromiss zu ermöglichen, kann die `IngestionBatching` Richtlinie verwendet werden.
Diese Richtlinie wird nur auf die in die Warteschlange eingereihte Erfassung angewendet und bietet die maximale erzwungene Verzögerung, wenn kleine blobdaten in einer Batch Verarbeitung zusammengefasst werden.

## <a name="details"></a>Details

Wie oben erläutert, gibt es eine optimale Größe von Daten, die in einem Massen Vorgang erfasst werden können.
Derzeit beträgt die Größe etwa 1 GB an nicht komprimierten Daten. Die Erfassung in BLOB-Daten, die wesentlich weniger Daten als die optimale Größe enthalten, ist nicht optimal. Daher werden bei der Erfassung in der Warteschlange solche kleinen BLOB in Batches zusammengefasst. Die Batch Verarbeitung erfolgt, bis die erste Bedingung erfüllt wird:

1. Die Gesamtgröße der Batch Daten erreicht die optimale Größe, oder
2. Die maximale Verzögerungszeit, Gesamtgröße oder Anzahl der von der Richtlinie zulässigen blobwerte wurde `IngestionBatching` erreicht.

Die `IngestionBatching` Richtlinie kann für Datenbanken oder Tabellen festgelegt werden. Wenn keine Richtlinie definiert ist, verwendet Kusto standardmäßig einen Standardwert von **5 Minuten** als maximale Verzögerungszeit, **1000** Elemente, Gesamtgröße von **1 g** für die Batch Verarbeitung.

> [!WARNING]
> Wenn diese Richtlinie auf einen sehr kleinen Wert festgelegt wird, ist dies eine Erhöhung der COGS (Kosten der verkauften Waren) des Clusters und die geringere Leistung. Darüber hinaus kann das verringern dieses Werts zu einer **höheren** effektiven End-to-End-Erfassungs Latenz führen, weil mehrere Erfassungs Prozesse parallel verwaltet werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Befehlsreferenz für ingestionbatching-Richtlinien](../management/batching-policy.md)
* [Bewährte Methoden für die Erfassung und Optimierung des Durchsatzes](../api/netfx/kusto-ingest-best-practices.md#optimizing-for-throughput)
