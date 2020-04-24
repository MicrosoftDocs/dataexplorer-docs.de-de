---
title: binary_all_xor() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird binary_all_xor() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: a1908fe874576281c9ba45f23709845473b5725c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517696"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor() (Aggregationsfunktion)

Kumuliert Werte `XOR` mit dem binären Vorgang pro Zusammenfassungsgruppe (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`binary_all_xor(` *Expr* zusammenfassen`)`

**Argumente**

* *Expr*: lange Zahl.

**Rückgabe**

Gibt einen Wert zurück, der `XOR` mit dem binären Vorgang über Datensätze pro Zusammenfassungsgruppe aggregiert wird (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

**Beispiel**

Herstellung von "Café-Food" `XOR` mit binären Operationen:

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