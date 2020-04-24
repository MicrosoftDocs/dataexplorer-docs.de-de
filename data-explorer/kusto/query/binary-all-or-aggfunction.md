---
title: binary_all_or() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird binary_all_or() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 5de240f606e53b26996ebfe11073170758233e2c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517713"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or() (Aggregationsfunktion)

Kumuliert Werte `OR` mit dem binären Vorgang pro Zusammenfassungsgruppe (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`binary_all_or(` *Expr* zusammenfassen`)`

**Argumente**

* *Expr*: lange Zahl.

**Rückgabe**

Gibt einen Wert zurück, der `OR` mit dem binären Vorgang über Datensätze pro Zusammenfassungsgruppe aggregiert wird (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

**Beispiel**

Herstellung von "Café-Food" `OR` mit binären Operationen:

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