---
title: Batches - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Batches in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd695a1e7ffd9980de2750d38ad9538eeb877538
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518019"
---
# <a name="batches"></a>Batches

Eine Abfrage kann mehrere tabellentabellarische Ausdrucksanweisungen enthalten, sofern`;`sie durch ein Semikolon ( ) begrenzt sind. Die Abfrage gibt dann mehrere tabellarische Ergebnisse zurück, die von den Tabellenausdrucksanweisungen erzeugt und entsprechend der Reihenfolge der Anweisungen im Abfragetext sortiert werden.

Die folgende Abfrage erzeugt z. B. zwei Tabellenergebnisse. Benutzer-Agent-Tools können dann diese Ergebnisse mit`Count of events in Florida` dem `Count of events in Guam`entsprechenden Namen anzeigen, der jedem (bzw. ) zugeordnet ist.

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

Batch ist besonders nützlich für Szenarien, in denen es eine gemeinsame Berechnung gibt, die von mehreren Unterabfragen gemeinsam genutzt wird, z. B. für Dashboards. Wenn die allgemeine Berechnung komplex ist, wird empfohlen, die Abfrage so zu konstruieren, dass sie nur einmal ausgeführt wird, indem die [Funktion materialize()](./materializefunction.md)verwendet wird:

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

Notizen:
* Bevorzugen Sie [`materialize`](materializefunction.md) Batching und über die Verwendung des [Gabel-Operators](forkoperator.md).