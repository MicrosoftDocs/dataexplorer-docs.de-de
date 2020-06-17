---
title: percentrank_tdigest ()-Azure Daten-Explorer
description: In diesem Artikel wird percentrank_tdigest () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: cafb52b7254041a18a9cae956ed338f45bc67a54
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818569"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

Berechnet den ungefähren Rang des Werts in einer Menge, wobei Rang als Prozentsatz der Größe des Satzes ausgedrückt wird.
Diese Funktion kann als Umkehrung des Perzentils angezeigt werden.

**Syntax**

`percentrank_tdigest(`*Tdigest* `,` *Expr*`)`

**Argumente**

* *Tdigest*: Ausdruck, der von [tdigest ()](tdigest-aggfunction.md) oder [tdigest_merge ()](tdigest-merge-aggfunction.md)generiert wurde.
* *Expr*: ein Ausdruck, der einen Wert darstellt, der für die Berechnung der prozentualen Bewertung verwendet wird.

**Rückgabe**

Der Prozentsatz des Werts in einem DataSet.

**Tipps**

1) Der Typ des zweiten Parameters und der Typ der Elemente in müssen `tdigest` identisch sein.

2) Der erste Parameter muss tdigest sein, der von [tdigest ()](tdigest-aggfunction.md) oder [tdigest_merge ()](tdigest-merge-aggfunction.md) generiert wurde.

**Beispiele**

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
