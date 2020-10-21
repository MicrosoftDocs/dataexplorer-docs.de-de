---
title: binary_all_or () (Aggregations Funktion)-Azure Daten-Explorer
description: In diesem Artikel wird binary_all_or () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: bff5d631493b9431d6e1a5fa88d3dd419147df96
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247971"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or () (Aggregations Funktion)

Akkumuliert Werte mithilfe der binären `OR` Operation pro Zusammenfassungs Gruppe (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`binary_all_or(` *expr* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Long-Nummer.

## <a name="returns"></a>Rückgabe

Gibt einen Wert zurück, der mithilfe der binären Operation für Datensätze pro Zusammenfassungs Gruppe aggregiert wird `OR` (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

## <a name="example"></a>Beispiel

Erstellen von "Café-Food" mithilfe von binären `OR` Vorgängen:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0x88888008,
  0x42000000,
  0x00767000,
  0x00000005, 
]
| summarize result = toupper(tohex(binary_all_or(num)))
```

|result|
|---|
|CAFEF00D|
