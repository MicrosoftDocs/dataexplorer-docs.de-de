---
title: IngestionBatching-Richtlinie - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die IngestionBatching-Richtlinie in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: fa3a4cfd512e3e37ba6b6abf8f8316d6ff12fdec
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522235"
---
# <a name="ingestionbatching-policy"></a>IngestionBatching-Richtlinie

## <a name="overview"></a>Übersicht

Während des Aufnahmeprozesses versucht Kusto, den Durchsatz zu optimieren, indem kleine Eingehende Datenblöcke zusammengestapelt werden, während sie auf die Aufnahme warten.
Diese Art der Stapelverarbeitung reduziert den Ressourcenverbrauch durch den Aufnahmeprozess und erfordert keine Ressourcen nach der Einnahme, um die kleinen Datenshards zu optimieren, die durch nicht batched insestion erzeugt werden.

Es hat jedoch einen Nachteil, batching vor der Einnahme durchzuführen, d. h. die Einführung einer erzwungenen Verzögerung, sodass die End-to-End-Zeit von der Anforderung der Erfassung von Daten, bis sie für die Abfrage bereit ist, größer ist.

Um die Kontrolle über diesen Kompromiss zu `IngestionBatching` ermöglichen, kann man die Richtlinie verwenden.
Diese Richtlinie wird nur auf die Aufnahme in die Warteschlange angewendet und bietet die maximale erzwungene Verzögerung, die beim Batchen kleiner Blobs zulässig ist.

## <a name="details"></a>Details

Wie oben erläutert, gibt es eine optimale Größe der Daten, die in großen Mengen aufgenommen werden.
Derzeit beträgt diese Größe etwa 1 GB unkomprimierter Daten. Die Aufnahme, die in Blobs durchgeführt wird, die viel weniger Daten als die optimale Größe enthalten, ist nicht optimal, und daher wird Kusto bei der Aufnahme in der Warteschlange solche kleinen Blobs zusammenstapeln. Die Stapelverarbeitung erfolgt, bis die erste Bedingung wahr wird:

1. Die Gesamtgröße der Batchdaten erreicht die optimale Größe, oder
2. Die maximale Verzögerungszeit, Gesamtgröße oder Anzahl der `IngestionBatching` von der Richtlinie zulässigen Blobs wird erreicht.

Die `IngestionBatching` Richtlinie kann für Datenbanken oder Tabellen festgelegt werden. Wenn nicht die Richtlinie definiert ist, verwendet Kusto standardmäßig einen Standardwert von **5 Minuten** als maximale Verzögerungszeit, **1000** Elemente, Gesamtgröße von **1G** für die Stapelverarbeitung.

> [!WARNING]
> Es wird empfohlen, dass Kunden, die diese Richtlinie festlegen möchten, sich zuerst an das Kusto-Ops-Team wenden. Die Auswirkung der Festlegung dieser Richtlinie auf einen sehr geringen Wert ist eine Erhöhung der COGS des Clusters und eine reduzierte Leistung. Darüber hinaus kann die Reduzierung dieses Werts im Limit tatsächlich zu einer **erhöhten** effektiven End-to-End-Aufnahmelatenz führen, da mehrere Aufnahmeprozesse parallel verwaltet werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [IngestionBatching-Richtlinienbefehle verweisen](../management/batching-policy.md)
* [Best Practices für die Verfilmung - Optimierung des Durchsatzes](../api/netfx/kusto-ingest-best-practices.md#optimizing-for-throughput)