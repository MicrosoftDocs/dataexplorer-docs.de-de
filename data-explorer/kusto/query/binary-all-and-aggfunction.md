---
title: binary_all_and () (Aggregations Funktion)-Azure Daten-Explorer
description: In diesem Artikel wird binary_all_and () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9086d00ecbc800174ce2b9cda2b4ae1ba59d52b5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349141"
---
# <a name="binary_all_and-aggregation-function"></a>binary_all_and () (Aggregations Funktion)

Akkumuliert Werte mithilfe der binären `AND` Operation pro Zusammenfassungs Gruppe (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`binary_all_and(` *expr* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Long-Nummer.

## <a name="returns"></a>Rückgabe

Gibt einen Wert zurück, der mithilfe der binären Operation für Datensätze pro Zusammenfassungs Gruppe aggregiert wird `AND` (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

## <a name="example"></a>Beispiel

Erstellen von "Café-Food" mithilfe von binären `AND` Vorgängen:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0xFFFFFFFF, 
  0xFFFFF00F,
  0xCFFFFFFD,
  0xFAFEFFFF,
]
| summarize result = toupper(tohex(binary_all_and(num)))
```

|result|
|---|
|CAFEF00D|
