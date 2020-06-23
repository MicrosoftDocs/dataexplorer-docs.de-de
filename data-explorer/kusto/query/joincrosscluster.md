---
title: 'Cluster übergreifender Join: Azure Daten-Explorer'
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
ms.openlocfilehash: dc22d44bd861a02d0db7fd0d8f7cc80ddcb5c8d4
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128455"
---
# <a name="cross-cluster-join"></a>Clusterübergreifender Join

::: zone pivot="azuredataexplorer"

Allgemeine Erörterung von Cluster übergreifenden Abfragen finden Sie unter [Cluster übergreifende oder datenbankübergreifende Abfragen](cross-cluster-or-database-queries.md) .

Es ist möglich, einen Join-Vorgang für Datasets in verschiedenen Clustern durchzuführen. Zum Beispiel:

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

Im obigen Beispiel handelt es sich bei dem Verknüpfungs Vorgang um einen Cluster übergreifenden Join, bei dem angenommen wird, dass der aktuelle Cluster nicht "somecluester" oder "SomeCluster2" ist.

Im folgenden Beispiel:

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

der Joinvorgang ist kein Cluster übergreifender Join, weil beide Operanden auf demselben Cluster stammen.

Wenn Kusto auf einen Cluster übergreifenden Join stößt, wird automatisch entschieden, wo der Joinvorgang selbst ausgeführt werden soll. Diese Entscheidung kann eines der drei möglichen Ergebnisse haben:

* Join-Vorgang für den Cluster des linken Operanden ausführen, rechter Operand wird zuerst von diesem Cluster abgerufen. (Beispiel: Join **(1)** wird im lokalen Cluster ausgeführt)
* Der Join-Vorgang wird auf dem Cluster des rechten Operanden ausgeführt, der linke Operand wird zuerst von diesem Cluster abgerufen. (Beispiel: Join **(2)** wird auf "SomeCluster2" ausgeführt)
* Lokales Ausführen von Verknüpfungs Vorgängen (d.h. auf dem Cluster, der die Abfrage empfangen hat), werden beide Operanden zuerst vom lokalen Cluster abgerufen.

Die tatsächliche Entscheidung hängt von der jeweiligen Abfrage ab. Die automatische joinremoting-Strategie ist (vereinfachte Version): "Wenn einer der Operanden lokal ist, wird der Join lokal ausgeführt. Wenn beide Operanden Remote sind, wird der Join im Cluster des rechten Operanden ausgeführt.

Manchmal kann die Leistung der Abfrage verbessert werden, wenn die automatische Remotingstrategie nicht befolgt wird. Führen Sie in diesem Fall einen Join-Vorgang auf dem Cluster mit dem größten Operanden aus.

Wenn beispielsweise **(1)** das von erzeugte DataSet `T | ...` wesentlich kleiner als das von erzeugte DataSet ist `cluster("SomeCluster").database("SomeDB").T2 | ...` , ist es effizienter, den Join-Vorgang für "somecluester" auszuführen.

Dieser Vorgang kann erfolgen, indem Sie den Kusto-joinremotinghinweis bereitstellen. Die Syntax ist:

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

Folgende zulässige Werte sind zulässig:`strategy`
* `left`-Join im Cluster des linken Operanden ausführen 
* `right`-Join im Cluster des rechten Operanden ausführen
* `local`-Join im Cluster des aktuellen Clusters ausführen
* `auto`-(Standard) lassen Sie die automatische Remoting-Entscheidung durch Kusto treffen

> [!Note]
> Der Join-Remoting-Hinweis wird von Kusto ignoriert, wenn die angedeutete Strategie nicht auf den Joinvorgang anwendbar ist.

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
