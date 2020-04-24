---
title: variancep() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt variancep() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 386244806a6fcb3f321eb1a6b40595dc71b2b413
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504589"
---
# <a name="variancep-aggregation-function"></a>variancep() (Aggregationsfunktion)

Berechnet die Varianz von *Expr* in der Gruppe unter Berücksichtigung der Gruppe als [Grundgesamtheit](https://en.wikipedia.org/wiki/Statistical_population). 

* Verwendete Formel: ![Alt-Text](./images/aggregations/variance-population.png "Varianz-Bevölkerung")

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`variancep(` *Expr* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 

**Rückgabe**

Der Varianzwert von *Expr* in der gesamten Gruppe.
 
**Beispiele**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[ 1, 2, 3, 4, 5]|2|