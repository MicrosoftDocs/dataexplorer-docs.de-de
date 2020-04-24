---
title: rank_tdigest() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird rank_tdigest() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: ea24213b0ca673c39f399c3a12cc54cd7d7f47d5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510539"
---
# <a name="rank_tdigest"></a>rank_tdigest()

Berechnet den ungefähren Rang des Wertes in einer Menge. Der `v` Wertrang in `S` einem Satz wird `S` als Anzahl der `v`Elemente `S` definiert, deren kleiner oder gleich ist, wird durch seinen tdigest dargestellt.

**Syntax**

`rank_tdigest(`*TDigest* `,` *Expr*`)`

**Argumente**

* *TDigest*: Ausdruck, der von [tdigest()](tdigest-aggfunction.md) oder [tdigest_merge()](tdigest-merge-aggfunction.md) erzeugt wurde
* *Expr*: Ausdruck, der einen Wert darstellt, der für die Rankingberechnung verwendet werden soll.

**Rückgabe**

Der Rang für jeden Wert in einem Datensatz.

**Tipps**

1) Die Werte, die Sie erhalten möchten, müssen vom gleichen Typ wie der tdigest sein.

**Beispiele**

In einer sortierten Liste (1-1000) ist der Rang von 685 der Index:

```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

Diese Abfrage berechnet den Rang des Wertes 4490 " über alle Schadenseigenschaftenkosten:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

Abrufen des geschätzten Prozentsatzes des Rangs (durch Dividieren durch die festgelegte Größe):

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


Das Perzentil 85 der Schäden Eigenschaften kosten ist 4490 .

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


