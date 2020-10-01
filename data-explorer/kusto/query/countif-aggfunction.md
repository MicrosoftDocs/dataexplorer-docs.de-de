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
ms.openlocfilehash: 9e81b4947f3a3a0b1102256cb7fd2f635ce4611b
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614973"
---
# <a name="countif-aggregation-function"></a>count () (Aggregations Funktion)

Gibt die Anzahl von Zeilen zurück, für die *Predicate* als `true` ausgewertet wird. Kann nur im Kontext von Aggregations Zusammenfassungen verwendet [werden.](summarizeoperator.md)

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

## <a name="see-also"></a>Weitere Informationen

[count ()](count-aggfunction.md) -Funktion, die Zeilen ohne Prädikat Ausdruck zählt.
