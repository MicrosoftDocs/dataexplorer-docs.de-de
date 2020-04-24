---
title: Startofyear() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Startofyear() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f91c749cc3833954d902eb4ebd7e230e32e3a991
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507207"
---
# <a name="startofyear"></a>startofyear()

Gibt den Anfang des Jahres zurück, das das Datum enthält, verschoben um einen Offset, sofern angegeben.

**Syntax**

`startofyear(`*Datum* `,`[*Offset*]`)`

**Argumente**

* `date`: Das Eingabedatum.
* `offset`: Eine optionale Anzahl von Offsetjahren ab dem Eingabedatum (ganzzahlig, default - 0). 

**Rückgabe**

Eine Datumszeit, die den Anfang des Jahres für den angegebenen *Datumswert* darstellt, mit dem Offset, falls angegeben.

**Beispiel**

```kusto
  range offset from -1 to 1 step 1
 | project yearStart = startofyear(datetime(2017-01-01 10:10:17), offset) 
```

|jahrStart|
|---|
|2016-01-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2018-01-01 00:00:00.0000000|