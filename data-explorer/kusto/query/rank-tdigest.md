---
title: rank_tdigest ()-Azure Daten-Explorer
description: In diesem Artikel wird rank_tdigest () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: bc0fff9d70c8260781332be61c701aaaca364555
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244837"
---
# <a name="rank_tdigest"></a>rank_tdigest()

Berechnet den ungefähren Rang des Werts in einer Menge. Der Rang des Werts `v` in einer Menge `S` ist als Anzahl von Elementen von definiert, `S` die kleiner oder gleich sind `v` , `S` wird durch die dargestellt `tdigest` .

## <a name="syntax"></a>Syntax

`rank_tdigest(`*`TDigest`*`,` *`Expr`*`)`

## <a name="arguments"></a>Argumente

* *Tdigest*: Ausdruck, der von [tdigest ()](tdigest-aggfunction.md) oder [tdigest_merge ()](tdigest-merge-aggfunction.md) generiert wurde.
* *Expr*: Ausdruck, der einen Wert darstellt, der für die Rang Folge Berechnung verwendet werden soll.

## <a name="returns"></a>Rückgabe

Der Rang für foreach-Werte in einem DataSet.

**Tipps**

1) Die Werte, deren Rang Sie erhalten möchten, müssen vom gleichen Typ sein wie das `tdigest` .

## <a name="examples"></a>Beispiele

In einer sortierten Liste (1-1000) ist der Rang von 685 der zugehörige Index:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

Diese Abfrage berechnet den Rang des Werts $4490 für alle Schäden der Schadens Eigenschaften:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

Erzielen des geschätzten Prozentsatzes des Rangs (durch Aufteilen durch die festgelegte Größe):

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


Das Quantil 85 der Kosten für die Schadens Eigenschaften beträgt $4490:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


