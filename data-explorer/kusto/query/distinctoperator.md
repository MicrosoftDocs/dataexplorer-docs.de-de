---
title: 'distinct-Operator: Azure Data Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der distinct-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 86b8617698f3708edcebbc1c2c4bd1732054600f
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513164"
---
# <a name="distinct-operator"></a>distinct-Operator

Generiert eine Tabelle mit der speziellen Kombination der bereitgestellten Spalten der Eingabetabelle. 

```kusto
T | distinct Column1, Column2
```

Generiert eine Tabelle mit der speziellen Kombination aller Spalten in der Eingabetabelle.

```kusto
T | distinct *
```

## <a name="example"></a>Beispiel

Zeigt die unterschiedliche Kombination aus Obst und Preis an.

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Zwei Tabellen. Eine weist Lieferanten, Obsttypen und Preise auf, wobei einige Kombinationen aus Obst und Preis wiederholt werden. In der zweiten Tabelle werden nur eindeutige Kombinationen aufgelistet.":::

**Hinweise**

* Im Gegensatz zu `summarize by ...` unterstützt der `distinct`-Operator die Bereitstellung eines Sternchens (`*`) als Gruppenschlüssel, was die Verwendung für breite Tabellen vereinfacht.
* Wenn die group by-Schlüssel hohe Kardinalitäten aufweisen, kann die Verwendung von `summarize by ...` mit der [Shuffle-Strategie](shufflequery.md) nützlich sein.