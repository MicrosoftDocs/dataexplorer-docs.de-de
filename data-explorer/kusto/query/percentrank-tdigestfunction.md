---
title: percentrank_tdigest() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden percentrank_tdigest() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: fe356ddb2e6301bbb283d2e6aa59b5c98f8bf3fe
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511185"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

Berechnet den ungefähren Rang des Wertes in einer Menge, in der Rang als Prozentsatz der Größe des Satzes ausgedrückt wird. Diese Funktion kann als Umkehrung des Perzentils betrachtet werden.

**Syntax**

`percentrank_tdigest(`*TDigest* `,` *Expr*`)`

**Argumente**

* *TDigest*: Ausdruck, der von [tdigest()](tdigest-aggfunction.md) oder [tdigest_merge()](tdigest-merge-aggfunction.md)erzeugt wurde.
* *Expr*: Ausdruck, der einen Wert darstellt, der für die Berechnung der prozentualen Rangfolge verwendet werden soll.

**Rückgabe**

Der prozentuale Rang des Werts in einem Dataset.

**Tipps**

1) Der Typ des zweiten Parameters und der Typ der Elemente im tdigest sollten identisch sein.

2) Erster Parameter sollte TDigest sein, das von [tdigest()](tdigest-aggfunction.md) oder [tdigest_merge()](tdigest-merge-aggfunction.md) generiert wurde.

**Beispiele**

Das Percentrank_tdigest() des Sachschadens im Wert von 4490 % beträgt 85 %:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|Column1|
|---|
|85.0015237192293|


Die Verwendung von Perzentil 85 über dem Schaden sollte 4490.

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|