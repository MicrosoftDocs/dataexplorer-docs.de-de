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
ms.openlocfilehash: ad90d9765b7ff708f7b40b65564b3944de5c84df
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245183"
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

**Notizen**

* Im Gegensatz dazu `summarize by ...` `distinct` unterstützt der-Operator die Bereitstellung eines Sternchen ( `*` ) als Gruppenschlüssel, was die Verwendung für Breite Tabellen erleichtert.
* Wenn die Group by-Schlüssel hohe Kardinalitäten aufweisen, kann die Verwendung von `summarize by ...` mit der [shuffle-Strategie](shufflequery.md) nützlich sein.