---
title: SUMIF () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird SUMIF () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7d97d31b2fb97d5541400bc0605ee40e83807b62
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83371899"
---
# <a name="sumif-aggregation-function"></a>SUMIF () (Aggregations Funktion)

Gibt eine Summe von *expr* zurück, für die das *Prädikat* ausgewertet wird `true` .

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

Sie können auch die [Sum ()](sum-aggfunction.md) -Funktion verwenden, die Zeilen ohne Prädikat Ausdruck summiert.

**Syntax**

`sumif(` *expr*- `,` *Prädikat* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck für die Aggregations Berechnung. 
* *Prädikat*: Prädikat: Wenn true, wird der berechnete Wert der *expr*der Summe hinzugefügt. 

**Rückgabe**

Der Sum-Wert von *expr* , für den das *Prädikat* ausgewertet wird `true` .

**Beispiel**

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize sumif(day_of_birth, strlen(name) > 4)
```

|sumif_day_of_birth|
|----|
|32|