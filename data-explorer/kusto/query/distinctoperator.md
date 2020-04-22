---
title: eindeutiger Operator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird ein eindeutiger Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4287ca48d3fb5006e67a9266ea05287a7d8a06f6
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030366"
---
# <a name="distinct-operator"></a>distinct-Operator

Erstellt eine Tabelle mit der deutlichen Kombination der bereitgestellten Spalten der Eingabetabelle. 

```kusto
T | distinct Column1, Column2
```

Erstellt eine Tabelle mit der eindeutigen Kombination aller Spalten in der Eingabetabelle.

```kusto
T | distinct *
```

**Beispiel**

Zeigt die deutliche Kombination von Obst und Preis.

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Unterschiedliche":::

**Hinweise**

* Im `summarize by ...`Gegensatz `distinct` zu unterstützt der Operator`*`die Bereitstellung eines Sternchens ( ) als Gruppenschlüssel, wodurch die Verwendung für breite Tabellen vereinfacht wird.
* Wenn die Gruppe nach Schlüsseln von `summarize by ...` hohen Kardinalitäten ist, könnte die Verwendung mit der [Shuffle-Strategie](shufflequery.md) nützlich sein.