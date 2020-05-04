---
title: 'Cluster übergreifender Join: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der Cluster übergreifende Join in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: bd2ebaa35de1997a96c6646c0fe0f7e248af2240
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737316"
---
# <a name="cross-cluster-join"></a>Cluster übergreifender Join

::: zone pivot="azuredataexplorer"

Allgemeine Erörterung von Cluster übergreifenden Abfragen finden Sie unter [Cluster-oder datenbankübergreifende Abfragen](cross-cluster-or-database-queries.md) .

Es ist möglich, einen Join-Vorgang für Datasets in verschiedenen Clustern auszuführen. Beispiel: 

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

In den obigen Beispielen wird ein Cluster übergreifender Join angenommen, vorausgesetzt, dass der aktuelle Cluster weder "somecluester" noch "SomeCluster2" ist.

Beachten Sie, dass im folgenden Beispiel

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

der Joinvorgang ist kein Cluster übergreifender Join, weil beide Operanden auf demselben Cluster stammen.

Wenn Kusto einen Cluster übergreifenden Join findet, entscheidet er automatisch, wo der Joinvorgang selbst ausgeführt werden soll. Diese Entscheidung kann eines der drei möglichen Ergebnisse haben:
* Join-Vorgang für den Cluster des linken Operanden ausführen, rechter Operand wird zuerst von diesem Cluster abgerufen. (Beispiel: Join **(1)** wird im lokalen Cluster ausgeführt)
* Der Join-Vorgang wird auf dem Cluster des rechten Operanden ausgeführt, der linke Operand wird zuerst von diesem Cluster abgerufen. (Beispiel: Join **(2)** wird auf "SomeCluster2" ausgeführt)
* Lokales Ausführen von Verknüpfungs Vorgängen (d. h. auf dem Cluster, der die Abfrage empfangen hat), werden beide Operanden zuerst vom lokalen Cluster abgerufen.

Die tatsächliche Entscheidung hängt von der jeweiligen Abfrage ab, die automatische Join-Remotingstrategie ist (vereinfachte Version): "Wenn einer der Operanden lokal ist, wird der Join lokal ausgeführt. Wenn beide Operanden auf dem Cluster des rechten Operanden ausgeführt werden, wird der Remote Beitritt ausgeführt.

Manchmal kann die Leistung der Abfrage erheblich verbessert werden, wenn die automatische Remotingstrategie nicht befolgt wird. Im Allgemeinen ist es am besten (von der Leistungs Perspektive), den Join-Vorgang auf dem Cluster mit dem größten Operanden auszuführen.

Wenn im Beispiel **(1)** das von ```T | ...``` erzeugte IT-DataSet wesentlich kleiner als das von ```cluster("SomeCluster").database("SomeDB").T2 | ...``` erstellte IT-DataSet ist, ist es effizienter, eine Joinoperation für "somecluester" auszuführen.

Dies kann erreicht werden, indem Sie den Kusto-joinremotinghinweis bereitstellen. Die Syntax ist:

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

Folgende zulässige Werte sind zulässig:*`strategy`*
* **`left`**-Join im Cluster des linken Operanden ausführen 
* **`right`**-Join im Cluster des rechten Operanden ausführen
* **`local`**-Join im Cluster des aktuellen Clusters ausführen
* **`auto`**-(Standard) lassen Sie die automatische Remoting-Entscheidung durch Kusto treffen

**Hinweis:** Der joinremoting-Hinweis wird von Kusto ignoriert, wenn die angedeutete Strategie nicht auf den Joinvorgang anwendbar ist.

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
