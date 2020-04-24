---
title: binary_all_and() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird binary_all_and() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 4dfe4a2881f100a4bea3e9d418022c75b2621087
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517747"
---
# <a name="binary_all_and-aggregation-function"></a>binary_all_and() (Aggregationsfunktion)

Kumuliert Werte `AND` mit dem binären Vorgang pro Zusammenfassungsgruppe (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`binary_all_and(` *Expr* zusammenfassen`)`

**Argumente**

* *Expr*: lange Zahl.

**Rückgabe**

Gibt einen Wert zurück, der `AND` mit dem binären Vorgang über Datensätze pro Zusammenfassungsgruppe aggregiert wird (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

**Beispiel**

Herstellung von "Café-Food" `AND` mit binären Operationen:

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