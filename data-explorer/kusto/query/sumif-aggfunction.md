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
ms.openlocfilehash: cd9900b5087ed0d6ae7e97d2f2dad809bb909331
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350807"
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