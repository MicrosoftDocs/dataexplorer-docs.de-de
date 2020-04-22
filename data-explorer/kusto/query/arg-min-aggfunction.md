---
title: arg_min() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird arg_min() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/12/2019
ms.openlocfilehash: 4531bd498cf9d9053d57d8b463c7b0adfcc9bf8d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663984"
---
# <a name="arg_min-aggregation-function"></a>arg_min() (Aggregationsfunktion)

Sucht eine Zeile in der Gruppe, die *ExprToMinimize*minimiert und den `*` Wert von *ExprToReturn* zurückgibt (oder die gesamte Zeile zurückgibt).

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`summarize`[`(`*NameExprToMinimieren von* `,` *NameExprToReturn* [`,` ...] `)=` `arg_min` ] `(` *ExprToMinimieren*, `*`  |  *ExprToReturn* [`,` ...]`)`

**Argumente**

* *ExprToMinimize*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 
* *ExprToReturn*: Ausdruck, der zum Zurückgeben des Werts verwendet wird, wenn *ExprToMinimize* minimal ist. Der zurückzugebende Ausdruck kann ein Platzhalter (*) sein, um alle Spalten der Eingabetabelle zurückzugeben.
* *NameExprToMinimize*: Ein optionaler Name für die Ergebnisspalte, die *ExprToMinimize*darstellt.
* *NameExprToReturn*: Zusätzliche optionale Namen für die Ergebnisspalten, die *ExprToReturn*darstellen.

**Rückgabe**

Sucht eine Zeile in der Gruppe, die *ExprToMinimize*minimiert und den `*` Wert von *ExprToReturn* zurückgibt (oder die gesamte Zeile zurückgibt).

**Beispiele**

Günstigster Lieferant für jedes Produkt:

```kusto
Supplies | summarize arg_min(Price, Supplier) by Product
```

Zeigen Sie alle Details, nicht nur den Lieferantennamen:

```kusto
Supplies | summarize arg_min(Price, *) by Product
```

Finden Sie die südlichste Stadt auf jedem Kontinent, mit ihrem Land:

```kusto
PageViewLog 
| summarize (latitude, min_lat_City, min_lat_country)=arg_min(latitude, City, country) 
    by continent
```

:::image type="content" source="images/aggregations/arg-min.png" alt-text="Arg min":::
