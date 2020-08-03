---
title: 'count () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Funktion "count ()" (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/02/2020
ms.openlocfilehash: 669978d8828f54926a8535f199ef7a9bc2ba7451
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515769"
---
# <a name="countif-aggregation-function"></a>count () (Aggregations Funktion)

Gibt die Anzahl von Zeilen zurück, für die *Predicate* als `true` ausgewertet wird.

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

Siehe auch- [count ()](count-aggfunction.md) -Funktion, die Zeilen ohne Prädikat Ausdruck zählt.

## <a name="syntax"></a>Syntax

`countif(` *Prädikat* zusammenfassen`)`

## <a name="arguments"></a>Argumente

*Predicate*: ein Ausdruck, der für die Aggregations Berechnung verwendet wird. Bei dem *Prädikat* kann es sich um einen beliebigen skalaren Ausdruck mit dem Rückgabetyp "bool" handeln

## <a name="returns"></a>Gibt zurück

Gibt die Anzahl von Zeilen zurück, für die *Predicate* als `true` ausgewertet wird.

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
| summarize countif(strlen(name) > 4)
```

|countif_|
|----|
|2|

