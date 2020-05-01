---
title: 'VarianceP () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt VarianceP () (Aggregations Funktion) in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 80f3f900649d2c4c36c7a50831e011f0ee018860
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618008"
---
# <a name="variancep-aggregation-function"></a>VarianceP () (Aggregations Funktion)

Berechnet die Varianz von *expr* in der Gruppe, wobei die Gruppe als [Population](https://en.wikipedia.org/wiki/Statistical_population)betrachtet wird. 

* Verwendete Formel:

:::image type="content" source="images/variancep-aggfunction/variance-population.png" alt-text="Varianz Population":::

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

**Syntax**

`variancep(` *expr* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 

**Rückgabe**

Der Varianz Wert von *expr* in der Gruppe.
 
**Beispiele**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2|