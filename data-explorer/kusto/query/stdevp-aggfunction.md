---
title: STDE VP () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird STDE VP () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: baafca4d8d5711d55838bceae817c36ecb0edd6f
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618569"
---
# <a name="stdevp-aggregation-function"></a>STDE VP () (Aggregations Funktion)

Berechnet die Standardabweichung von *expr* in der Gruppe, wobei die Gruppe als [Population](https://en.wikipedia.org/wiki/Statistical_population)betrachtet wird. 

* Verwendete Formel:

:::image type="content" source="images/stdevp-aggfunction/stdev-population.png" alt-text="StDev-Population":::

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

**Syntax**

`stdevp(` *expr* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 

**Rückgabe**

Der Standardwert für die Abweichung von *expr* in der Gruppe.
 
**Beispiele**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[1, 2, 3, 4, 5]|1.4142135623731|