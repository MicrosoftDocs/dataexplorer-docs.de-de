---
title: minif() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird minif() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0aad254ec01e83bdb07734e5b309c1450512b446
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512358"
---
# <a name="minif-aggregation-function"></a>minif() (Aggregationsfunktion)

Gibt den Mindestwert für die Gruppe zurück, `true`für die *Prädikat* ausgewertet wird.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

Siehe auch - [min()-Funktion,](min-aggfunction.md) die den Minimalwert in der Gruppe ohne Prädikatsausdruck zurückgibt.

**Syntax**

`summarize``minif(``,`*Expr-Prädikat* *Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird.
* *Prädikat*: Prädikat, dass, wenn true, der berechnete *Expr-Wert* auf Minimum überprüft wird.

**Rückgabe**

Der Mindestwert von *Expr* in der Gruppe, `true`für die *Predikat* ausgewertet wird, .

**Beispiele**

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|