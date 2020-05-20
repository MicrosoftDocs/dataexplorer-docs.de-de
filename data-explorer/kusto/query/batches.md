---
title: 'Batches: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt Batches in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e5a38d53fb9b28fc7da0ddf71132e3a047e8188e
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550332"
---
# <a name="batches"></a>Batches

Eine Abfrage kann mehrere tabellarische Ausdrucks Anweisungen enthalten, solange Sie durch ein Semikolon () getrennt sind `;` . Die Abfrage gibt dann mehrere tabellarische Ergebnisse zurück. Die Ergebnisse werden von den Anweisungen für tabellarische Ausdrücke erzeugt und entsprechend der Reihenfolge der Anweisungen im Abfragetext sortiert.

Die folgende Abfrage erzeugt beispielsweise zwei tabellarische Ergebnisse. Benutzer-Agent-Tools können diese Ergebnisse dann mit dem entsprechenden Namen anzeigen, der den einzelnen ( `Count of events in Florida` `Count of events in Guam` bzw.) zugeordnet ist.

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

Batch ist nützlich für Szenarien, in denen eine gängige Berechnung von mehreren Unterabfragen, z. b. für Dashboards, gemeinsam genutzt wird. Wenn die allgemeine Berechnung Komplex ist, verwenden Sie die [Materialize ()-Funktion](./materializefunction.md) , und erstellen Sie die Abfrage so, dass Sie nur einmal ausgeführt wird:

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

Notizen:
* Ziehen Sie die Batch Verarbeitung und [`materialize`](materializefunction.md) die Verwendung des [Fork-Operators](forkoperator.md)vor.
