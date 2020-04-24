---
title: varianceif() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird varianceif() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9dfebb3796f07dec6c91d36d788a018f84f70961
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504674"
---
# <a name="varianceif-aggregation-function"></a>varianceif() (Aggregationsfunktion)

Berechnet die [Varianz](variance-aggfunction.md) von *Expr* in der Gruppe, `true`für die *Prädikat* ausgewertet wird.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`varianceif(` *Expr*`, `*Predikat* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 
* *Prädikat*: Prädikat, dass, wenn true, der berechnete *Expr-Wert* der Varianz hinzugefügt wird.

**Rückgabe**

Der Varianzwert von *Expr* in der Gruppe, `true`in der *Predikat* ausgewertet wird, ist .
 
**Beispiele**

```kusto
range x from 1 to 100 step 1
| summarize varianceif(x, x%2 == 0)

```

|varianceif_x|
|---|
|850|