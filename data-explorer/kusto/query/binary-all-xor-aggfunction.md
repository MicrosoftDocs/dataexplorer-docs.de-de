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
ms.openlocfilehash: bc4b0bc8a02dd3a8d2a39ffdd27db5817eb8ffdb
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225240"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor () (Aggregations Funktion)

Akkumuliert Werte mithilfe der binären `XOR` Operation pro Zusammenfassungs Gruppe (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

**Syntax**

`binary_all_xor(` *expr* zusammenfassen`)`

**Argumente**

* *Expr*: Long-Nummer.

**Rückgabe**

Gibt einen Wert zurück, der mithilfe der binären Operation für Datensätze pro Zusammenfassungs Gruppe aggregiert wird `XOR` (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

**Beispiel**

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
