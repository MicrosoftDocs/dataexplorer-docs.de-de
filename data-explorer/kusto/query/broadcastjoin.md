---
title: 'Broadcast-Join: Azure-Daten-Explorer'
description: In diesem Artikel wird der Broadcast Beitritt in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0cbcd71cc4582a4f5dd966001ae32fd4cedf0c1e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245288"
---
# <a name="broadcast-join"></a>Broadcastjoin

Heute werden reguläre Joins auf einem einzelnen Cluster Knoten ausgeführt.
Broadcast Join ist eine Ausführungs Strategie von Join, mit der es über Cluster Knoten verteilt wird. Diese Strategie ist nützlich, wenn die linke Seite des Joins klein ist (bis zu 100 K Datensätze). In diesem Fall ist der Broadcast Join leistungsfähiger als regulärer Join.

Wenn die linke Seite des Joins ein kleines DataSet ist, können Sie Join im Broadcast Modus ausführen, indem Sie die folgende Syntax verwenden (Hint. Strategy = Broadcast):

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

Die Leistungsverbesserung wird in Szenarien, in denen der Join von anderen Operatoren wie folgt, deutlicher bemerkbar machen `summarize` . beispielsweise in dieser Abfrage:

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```