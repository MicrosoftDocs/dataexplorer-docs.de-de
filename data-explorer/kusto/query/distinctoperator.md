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
ms.openlocfilehash: ffdd20c6d0c078cd3a7ecaa0d0fb2dac131ddda5
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941773"
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