---
title: SUMIF () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird SUMIF () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a62ff98810996aff1352b959768385bec76e8ba8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251343"
---
# <a name="sumif-aggregation-function"></a>SUMIF () (Aggregations Funktion)

Gibt eine Summe von *expr* zurück, für die das *Prädikat* ausgewertet wird `true` .

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

Sie können auch die [Sum ()](sum-aggfunction.md) -Funktion verwenden, die Zeilen ohne Prädikat Ausdruck summiert.

## <a name="syntax"></a>Syntax

`sumif(` *expr*- `,` *Prädikat* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck für die Aggregations Berechnung. 
* *Prädikat*: Prädikat: Wenn true, wird der berechnete Wert der *expr*der Summe hinzugefügt. 

## <a name="returns"></a>Rückgabe

Der Sum-Wert von *expr* , für den das *Prädikat* ausgewertet wird `true` .

## <a name="example"></a>Beispiel

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