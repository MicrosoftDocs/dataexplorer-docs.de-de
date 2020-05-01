---
title: 'StDev () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird StDev () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3a29621a18a364145585022b1f0651100cadab1c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618535"
---
# <a name="stdev-aggregation-function"></a>StDev () (Aggregations Funktion)

Berechnet die Standardabweichung von *expr* in der Gruppe, wobei die Gruppe als [Beispiel](https://en.wikipedia.org/wiki/Sample_%28statistics%29)betrachtet wird. 

* Verwendete Formel:

:::image type="content" source="images/stdev-aggfunction/stdev-sample.png" alt-text="StDev-Beispiel":::

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

**Syntax**

`stdev(` *expr* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 

**Rückgabe**

Der Standardwert für die Abweichung von *expr* in der Gruppe.
 
**Beispiele**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[1, 2, 3, 4, 5]|1.58113883008419|