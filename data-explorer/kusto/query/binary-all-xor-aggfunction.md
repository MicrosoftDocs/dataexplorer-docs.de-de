---
title: binary_all_xor () (Aggregations Funktion)-Azure Daten-Explorer
description: In diesem Artikel wird binary_all_xor () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: c2fab7abbcebdc97f6f1c394f840bf6e232db03a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349107"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor () (Aggregations Funktion)

Akkumuliert Werte mithilfe der binären `XOR` Operation pro Zusammenfassungs Gruppe (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`binary_all_xor(` *expr* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Long-Nummer.

## <a name="returns"></a>Gibt zurück

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
