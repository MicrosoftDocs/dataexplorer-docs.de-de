---
title: startofday() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt startofday() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6517b50ca880085761212ae9037cee96d20a3269
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507275"
---
# <a name="startofday"></a>startofday()

Gibt den Anfang des Tages zurück, der das Datum enthält, verschoben um einen Offset, sofern angegeben.

**Syntax**

`startofday(`*Datum* `,`[*Offset*]`)`

**Argumente**

* `date`: Das Eingabedatum.
* `offset`: Eine optionale Anzahl von Offsettagen ab dem Eingabedatum (ganzzahlig, default - 0). 

**Rückgabe**

Eine Datumszeit, die den Anfangsbeginn des Tages für den angegebenen *Datumswert* mit dem Offset darstellt, falls angegeben.

**Beispiel**

```kusto
  range offset from -1 to 1 step 1
 | project dayStart = startofday(datetime(2017-01-01 10:10:17), offset) 
```

|dayStart|
|---|
|2016-12-31 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-02 00:00:00.0000000|