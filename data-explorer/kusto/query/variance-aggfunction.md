---
title: 'Varianz () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Varianz () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f8afae2413d65618cf6b6e2f400df2500b06078
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618043"
---
# <a name="variance-aggregation-function"></a>Varianz () (Aggregations Funktion)

Berechnet die Varianz der *expr* in der Gruppe, wobei die Gruppe als [Beispiel](https://en.wikipedia.org/wiki/Sample_%28statistics%29)betrachtet wird. 

* Verwendete Formel:

:::image type="content" source="images/variance-aggfunction/variance-sample.png" alt-text="Varianz Beispiel":::

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

**Syntax**

`variance(` *expr* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 

**Rückgabe**

Der Varianz Wert von *expr* in der Gruppe.
 
**Beispiele**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2.5|