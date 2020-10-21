---
title: binary_all_xor () (Aggregations Funktion)-Azure Daten-Explorer
description: In diesem Artikel wird binary_all_xor () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: 7aa83f7c214c7bc45892ff1064a09dd84240b5f4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246732"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor () (Aggregations Funktion)

Akkumuliert Werte mithilfe der binären `XOR` Operation pro Zusammenfassungs Gruppe (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`binary_all_xor(` *expr* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Long-Nummer.

## <a name="returns"></a>Rückgabe

Gibt einen Wert zurück, der mithilfe der binären Operation für Datensätze pro Zusammenfassungs Gruppe aggregiert wird `XOR` (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

## <a name="example"></a>Beispiel

Erstellen von "Café-Food" mithilfe von binären `XOR` Vorgängen:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0x44404440,
  0x1E1E1E1E,
  0x90ABBA09,
  0x000B105A,
]
| summarize result = toupper(tohex(binary_all_xor(num)))
```

|result|
|---|
|CAFEF00D|
