---
title: rank_tdigest ()-Azure Daten-Explorer
description: In diesem Artikel wird rank_tdigest () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: a849cd496d41ad473768b3f267639eaf8c467880
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741777"
---
# <a name="rank_tdigest"></a>rank_tdigest()

Berechnet den ungefähren Rang des Werts in einer Menge. Der Rang des `v` Werts in einer `S` Menge ist als Anzahl von Elementen von `S` definiert, die kleiner oder gleich `v`sind `S` , wird durch die `tdigest`dargestellt.

**Syntax**

`rank_tdigest(`*`TDigest`*`,` *`Expr`*`)`

**Argumente**

* *Tdigest*: Ausdruck, der von [tdigest ()](tdigest-aggfunction.md) oder [tdigest_merge ()](tdigest-merge-aggfunction.md) generiert wurde.
* *Expr*: Ausdruck, der einen Wert darstellt, der für die Rang Folge Berechnung verwendet werden soll.

**Rückgabe**

Der Rang für foreach-Werte in einem DataSet.

**Tipps**

1) Die Werte, deren Rang Sie erhalten möchten, müssen vom gleichen Typ sein wie das `tdigest`.

**Beispiele**

In einer sortierten Liste (1-1000) ist der Rang von 685 der zugehörige Index:

```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

Diese Abfrage berechnet den Rang des Werts $4490 für alle Schäden der Schadens Eigenschaften:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

Erzielen des geschätzten Prozentsatzes des Rangs (durch Aufteilen durch die festgelegte Größe):

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


Das Quantil 85 der Kosten für die Schadens Eigenschaften beträgt $4490:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


