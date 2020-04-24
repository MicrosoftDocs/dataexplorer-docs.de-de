---
title: variance() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt variance() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b1d2ea47060ecede855a3fb419bbbfe2bf0b538
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504708"
---
# <a name="variance-aggregation-function"></a>varianz() (Aggregationsfunktion)

Berechnet die Varianz von *Expr* in der Gruppe unter Berücksichtigung der Gruppe als [Beispiel](https://en.wikipedia.org/wiki/Sample_%28statistics%29). 

* Verwendete Formel: ![Alt-Text](./images/aggregations/variance-sample.png "Varianz-Probe")

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`variance(` *Expr* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 

**Rückgabe**

Der Varianzwert von *Expr* in der gesamten Gruppe.
 
**Beispiele**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[ 1, 2, 3, 4, 5]|2.5|