---
title: endofday() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt endofday() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a25f6b9c4ba660fbb8a50390d9d6920ff21caeb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515877"
---
# <a name="endofday"></a>endofday()

Gibt das Ende des Tages zurück, der das Datum enthält, verschoben um einen Offset, sofern angegeben.

**Syntax**

`endofday(`*Datum* `,`[*Offset*]`)`

**Argumente**

* `date`: Das Eingabedatum.
* `offset`: Eine optionale Anzahl von Offsettagen ab dem Eingabedatum (ganzzahlig, default - 0).

**Rückgabe**

Eine Datumszeit, die das Ende des Tages für den angegebenen *Datumswert* mit dem Offset darstellt, falls angegeben.

**Beispiel**

```kusto
  range offset from -1 to 1 step 1
 | project dayEnd = endofday(datetime(2017-01-01 10:10:17), offset) 
```

|dayEnd|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-01 23:59:59.9999999|
|2017-01-02 23:59:59.9999999|