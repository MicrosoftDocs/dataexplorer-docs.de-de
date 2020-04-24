---
title: startofweek() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt startofweek() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9705b586a0b8c5161b7d4c27735f644b6982c707
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507224"
---
# <a name="startofweek"></a>startofweek()

Gibt den Anfang der Woche zurück, der das Datum enthält, verschoben um einen Offset, sofern angegeben.

Der Wochenbeginn gilt als Sonntag.

**Syntax**

`startofweek(`*Datum* `,`[*Offset*]`)`

**Argumente**

* `date`: Das Eingabedatum.
* `offset`: Eine optionale Anzahl von Offsetwochen ab dem Eingabedatum (ganzzahlig, default - 0).

**Rückgabe**

Eine Datumszeit, die den Wochenanfang für den angegebenen *Datumswert* mit dem Offset darstellt, falls angegeben.

**Beispiel**

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|weekStart|
|---|
|2016-12-25 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-08 00:00:00.0000000|