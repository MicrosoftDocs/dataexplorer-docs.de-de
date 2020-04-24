---
title: avgif() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt avgif() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 61352be628b7c5a05085c092d0c022deaa0d9b6e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518257"
---
# <a name="avgif-aggregation-function"></a>avgif() (Aggregationsfunktion)

Berechnet den [Durchschnitt](avg-aggfunction.md) von *Expr* in der Gruppe, `true`für die *Predikat* ausgewertet wird.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`avgif(` *Expr*`, `*Predikat* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. Datensätze `null` mit Werten werden ignoriert und nicht in die Berechnung einbezogen.
* *Prädikat*: Prädikat, dass, wenn true, der berechnete *Expr-Wert* zum Durchschnitt addiert wird.

**Rückgabe**

Der Durchschnittliche Wert von *Expr* in der `true`Gruppe, in der *Predikat* ausgewertet wird, ist .
 
**Beispiele**

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|