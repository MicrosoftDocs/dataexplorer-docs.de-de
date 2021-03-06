---
title: percentrank_tdigest ()-Azure Daten-Explorer
description: In diesem Artikel wird percentrank_tdigest () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: dd784d8968b45a735bd2df840a09c349e2fdcbd2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249693"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

Berechnet den ungefähren Rang des Werts in einer Menge, wobei Rang als Prozentsatz der Größe des Satzes ausgedrückt wird.
Diese Funktion kann als Umkehrung des Perzentils angezeigt werden.

## <a name="syntax"></a>Syntax

`percentrank_tdigest(`*Tdigest* `,` *Expr*`)`

## <a name="arguments"></a>Argumente

* *Tdigest*: Ausdruck, der von [tdigest ()](tdigest-aggfunction.md) oder [tdigest_merge ()](tdigest-merge-aggfunction.md)generiert wurde.
* *Expr*: ein Ausdruck, der einen Wert darstellt, der für die Berechnung der prozentualen Bewertung verwendet wird.

## <a name="returns"></a>Rückgabe

Der Prozentsatz des Werts in einem DataSet.

**Tipps**

1) Der Typ des zweiten Parameters und der Typ der Elemente in müssen `tdigest` identisch sein.

2) Der erste Parameter muss tdigest sein, der von [tdigest ()](tdigest-aggfunction.md) oder [tdigest_merge ()](tdigest-merge-aggfunction.md) generiert wurde.

## <a name="examples"></a>Beispiele

Die percentrank_tdigest () der Schadens Eigenschaft mit dem Wert $4490 beträgt ~ 85%:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|Column1|
|---|
|85.0015237192293|


Die Verwendung von Perzentil 85 über der Eigenschaft "Schaden" sollte $4490 betragen:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|
