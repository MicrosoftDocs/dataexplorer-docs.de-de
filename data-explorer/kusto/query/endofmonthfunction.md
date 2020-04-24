---
title: Ende des Monats () - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Endofmonth() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ebab067c730e3cd61c84ae33eba4e49d7f0b0c0c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515860"
---
# <a name="endofmonth"></a>endofmonth()

Gibt das Ende des Monats zurück, der das Datum enthält, verschoben um einen Offset, sofern angegeben.

**Syntax**

`endofmonth(`*Datum* `,`[*Offset*]`)`

**Argumente**

* `date`: Das Eingabedatum.
* `offset`: Eine optionale Anzahl von Offset-Monaten ab dem Eingabedatum (ganzzahlig, default - 0).

**Rückgabe**

Eine Datumszeit, die das Ende des Monats für den angegebenen *Datumswert* darstellt, mit dem Offset, falls angegeben.

**Beispiel**

```kusto
  range offset from -1 to 1 step 1
 | project monthEnd = endofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|monthEnd|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-31 23:59:59.9999999|
|2017-02-28 23:59:59.9999999|