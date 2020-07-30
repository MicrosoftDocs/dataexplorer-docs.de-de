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
ms.openlocfilehash: 70118746a079d76b1b6729bed3aae96c48399538
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338659"
---
# <a name="variancep-aggregation-function"></a>VarianceP () (Aggregations Funktion)

Berechnet die Varianz von *expr* in der Gruppe, wobei die Gruppe als [Population](https://en.wikipedia.org/wiki/Statistical_population)betrachtet wird. 

* Verwendete Formel:

:::image type="content" source="images/variancep-aggfunction/variance-population.png" alt-text="Varianz Population":::

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`variancep(` *expr* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 

## <a name="returns"></a>Gibt zurück

Der Varianz Wert von *expr* in der Gruppe.
 
## <a name="examples"></a>Beispiele

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2|