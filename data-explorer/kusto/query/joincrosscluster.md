---
title: Clusterübergreifende Verknüpfung - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die clusterübergreifende Teilnahme an Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1199b148fa295ac17417bbf590a73bfc9400a710
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765936"
---
# <a name="cross-cluster-join"></a>Clusterübergreifende Verknüpfung

::: zone pivot="azuredataexplorer"

Allgemeine Informationen zu clusterübergreifenden Abfragen finden Sie unter [Cluster- oder Datenbankübergreifende Abfragen](cross-cluster-or-database-queries.md)

Es ist möglich, Einen Join-Vorgang für Datasets auszuführen, die sich in verschiedenen Clustern befinden. Beispiel: 

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

In den obigen Beispielen ist eine Clusterübergreifende Verknüpfung, die davon ausgeht, dass der aktuelle Cluster weder "SomeCluster" noch "SomeCluster2" ist.

Beachten Sie, dass im folgenden Beispiel

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

Der Verknüpfungsvorgang ist keine Clusterübergreifende Verknüpfung, da beide Operanden aus demselben Cluster stammen.

Wenn Kusto auf die Cluster-Übergreifende Verknüpfung trifft, entscheidet es automatisch, wo der Join-Vorgang selbst ausgeführt werden soll. Diese Entscheidung kann eines der drei möglichen Ergebnisse haben:
* Führen Sie den Join-Vorgang auf dem Cluster des linken Operanden aus, der rechte Operand wird zuerst von diesem Cluster abgerufen. (Join in Beispiel **(1)** wird auf dem lokalen Cluster ausgeführt)
* Führen Sie den Join-Vorgang auf dem Cluster des rechten Operanden aus, der linke Operand wird zuerst von diesem Cluster abgerufen. (Join in Beispiel **(2)** wird auf dem "SomeCluster2" ausgeführt)
* Ausführen des Join-Vorgangs lokal (d. h. auf dem Cluster, der die Abfrage empfangen hat), werden beide Operanden zuerst vom lokalen Cluster abgerufen.

Die eigentliche Entscheidung hängt von der spezifischen Abfrage ab, automatische Join-Remoting-Strategie ist (vereinfachte Version): "Wenn einer der Operanden lokal ist, wird die Verknüpfung lokal ausgeführt. Wenn beide Operanden Remote-Join sind, wird auf dem Cluster des rechten Operandens ausgeführt."

Manchmal kann die Leistung der Abfrage erheblich verbessert werden, wenn die automatische Remoting-Strategie nicht befolgt wird. Im Allgemeinen ist es am besten (vom Standpunkt der Leistung), join operation auf dem Cluster des größten Operanden auszuführen.

Wenn es in Beispiel **(1)** ein ```T | ...``` von dem ```cluster("SomeCluster").database("SomeDB").T2 | ...``` erstelltes Dataset viel kleiner ist als ein von ihm erzeugtes Dataset, ist es effizienter, den Join-Vorgang auf "SomeCluster" auszuführen.

Dies kann erreicht werden, indem Kusto Join-Remoting-Hinweis gegeben wird. Die Syntax ist:

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

Im Folgenden sind rechtliche Werte für*`strategy`*
* **`left`**- Join auf dem Cluster des linken Operanden ausführen 
* **`right`**- Join auf dem Cluster des rechten Operanden ausführen
* **`local`**- Join auf dem Cluster des aktuellen Clusters ausführen
* **`auto`**- (Standard) lassen Sie Kusto die automatische Remoting-Entscheidung treffen

**Hinweis:** Join-Remoting-Hinweis wird von Kusto ignoriert, wenn die angedeutete Strategie nicht auf den Join-Vorgang anwendbar ist.

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
