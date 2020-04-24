---
title: Broadcast-Beitritt - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Broadcast Join in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 72c89bf2160f8f5b735fd8c93f9519feae9114d9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517305"
---
# <a name="broadcast-join"></a>Broadcastjoin

Heute werden die regulären Verknüpfungen auf einem einzelnen Clusterknoten ausgeführt.
Broadcast-Join ist eine Ausführungsstrategie der Verknüpfung, die sie über Clusterknoten verteilt, und es ist nützlich, wenn die linke Seite der Verknüpfung klein ist (bis zu 100K-Datensätze), in diesem Fall ist die Verknüpfung leistungsfähiger als die reguläre Verknüpfung.

Wenn die linke Seite der Verknüpfung ein kleines Dataset ist, können Sie Join im Broadcast-Modus mit der folgenden Syntax ausführen (hint.strategy = Broadcast):

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

Die Leistungsverbesserung wird in Szenarien, in denen auf `summarize`die Verknüpfung von anderen Operatoren wie gefolgt wird, deutlicher. z. B. in dieser Abfrage:

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```