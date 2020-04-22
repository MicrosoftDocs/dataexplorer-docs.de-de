---
title: stdev() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt stdev() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d54af583db6f7aca0b436040c453249207a5ae59
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663099"
---
# <a name="stdev-aggregation-function"></a>stdev() (Aggregationsfunktion)

Berechnet die Standardabweichung von *Expr* in der Gruppe unter Berücksichtigung der Gruppe als [Beispiel](https://en.wikipedia.org/wiki/Sample_%28statistics%29). 

* Verwendete Formel:

:::image type="content" source="images/aggregations/stdev-sample.png" alt-text="Stdev-Beispiel":::

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`stdev(` *Expr* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 

**Rückgabe**

Der Standardabweichungswert von *Expr* in der gesamten Gruppe.
 
**Beispiele**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[ 1, 2, 3, 4, 5]|1.58113883008419|