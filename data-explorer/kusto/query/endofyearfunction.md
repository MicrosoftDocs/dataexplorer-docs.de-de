---
title: Endofyear() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Endofyear() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d4a14d1cc42d5b9116e8a144e2b67fb74c1932ee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515775"
---
# <a name="endofyear"></a>endofyear()

Gibt das Ende des Jahres zurück, das das Datum enthält, verschoben um einen Offset, sofern angegeben.

**Syntax**

`endofyear(`*Datum* `,`[*Offset*]`)`

**Argumente**

* `date`: Das Eingabedatum.
* `offset`: Eine optionale Anzahl von Offsetjahren ab dem Eingabedatum (ganzzahlig, default - 0).

**Rückgabe**

Eine Datumszeit, die das Ende des Jahres für den angegebenen *Datumswert* darstellt, mit dem Offset, falls angegeben.

**Beispiel**

```kusto
  range offset from -1 to 1 step 1
 | project yearEnd = endofyear(datetime(2017-01-01 10:10:17), offset) 
```

|jahrEnde|
|---|
|2016-12-31 23:59:59.9999999|
|2017-12-31 23:59:59.9999999|
|2018-12-31 23:59:59.9999999|