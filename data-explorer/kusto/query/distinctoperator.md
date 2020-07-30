---
title: 'unterschiedlicher Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die unterschiedlichen Operatoren in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 233d3fdb0e25720b860a0c11515daec7c597dadd
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348342"
---
# <a name="distinct-operator"></a>distinct-Operator

Erzeugt eine Tabelle mit der eindeutigen Kombination der angegebenen Spalten der Eingabe Tabelle. 

```kusto
T | distinct Column1, Column2
```

Erzeugt eine Tabelle mit der eindeutigen Kombination aller Spalten in der Eingabe Tabelle.

```kusto
T | distinct *
```

## <a name="example"></a>Beispiel

Zeigt die unterschiedliche Kombination aus Obst und Preis an.

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Distinct":::

**Hinweise**

* Im Gegensatz dazu `summarize by ...` `distinct` unterstützt der-Operator die Bereitstellung eines Sternchen ( `*` ) als Gruppenschlüssel, was die Verwendung für Breite Tabellen erleichtert.
* Wenn die Group by-Schlüssel hohe Kardinalitäten aufweisen, kann die Verwendung von `summarize by ...` mit der [shuffle-Strategie](shufflequery.md) nützlich sein.