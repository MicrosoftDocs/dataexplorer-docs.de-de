---
title: 'unterschiedlicher Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die unterschiedlichen Operatoren in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 86b8617698f3708edcebbc1c2c4bd1732054600f
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513164"
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

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Zwei Tabellen. Eine weist Lieferanten, Frucht Typen und Preise auf, wobei einige Kombinationen aus Obst Preis wiederholt werden. In der zweiten Tabelle werden nur eindeutige Kombinationen aufgelistet.":::

**Hinweise**

* Im Gegensatz dazu `summarize by ...` `distinct` unterstützt der-Operator die Bereitstellung eines Sternchen ( `*` ) als Gruppenschlüssel, was die Verwendung für Breite Tabellen erleichtert.
* Wenn die Group by-Schlüssel hohe Kardinalitäten aufweisen, kann die Verwendung von `summarize by ...` mit der [shuffle-Strategie](shufflequery.md) nützlich sein.