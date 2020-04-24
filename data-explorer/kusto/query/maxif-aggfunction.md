---
title: maxif() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt maxif() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9be25615f9da61aec6b4d56543f624fa0c24c1c4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512443"
---
# <a name="maxif-aggregation-function"></a>maxif() (Aggregationsfunktion)

Gibt den Maximalwert für die Gruppe zurück, `true`für die *Predikat* ausgewertet wird.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

Siehe auch - [max()-Funktion,](max-aggfunction.md) die den maximalen Wert in der Gruppe ohne Prädikatsausdruck zurückgibt.

**Syntax**

`summarize``maxif(``,`*Expr-Prädikat* *Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 
* *Prädikat*: Prädikat, dass, wenn true, der berechnete *Expr-Wert* auf Maximum überprüft wird.

**Rückgabe**

Der maximale Wert von *Expr* in der Gruppe, `true`für die *Predikat* ausgewertet wird, .

**Beispiele**

```kusto
range x from 1 to 100 step 1
| summarize maxif(x, x < 50)
```

|maxif_x|
|---|
|49|