---
title: stdevif() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt stdevif() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4a64cf1bb69860a2a8bd64de91cb00c2f0ec296f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506969"
---
# <a name="stdevif-aggregation-function"></a>stdevif() (Aggregationsfunktion)

Berechnet den [Stdev](stdev-aggfunction.md) von *Expr* in der Gruppe, `true`für die *Predikat* ausgewertet wird.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`stdevif(` *Expr*`, `*Predikat* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 
* *Prädikat*: Prädikat, dass, wenn true, der berechnete *Expr-Wert* zur Standardabweichung hinzugefügt wird.

**Rückgabe**

Der Standardabweichungswert von *Expr* in der Gruppe, in der *Predikat* ausgewertet wird, `true`ist .
 
**Beispiele**

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|