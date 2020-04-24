---
title: Startofmonth() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Startofmonth() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e220919f6b09dbcd2519c67f48148a6375395e7b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507241"
---
# <a name="startofmonth"></a>startofmonth()

Gibt den Anfang des Monats zurück, der das Datum enthält, verschoben um einen Offset, sofern angegeben.

**Syntax**

`startofmonth(`*Datum* `,`[*Offset*]`)`

**Argumente**

* `date`: Das Eingabedatum.
* `offset`: Eine optionale Anzahl von Offset-Monaten ab dem Eingabedatum (ganzzahlig, default - 0).

**Rückgabe**

Eine Datumszeit, die den Monatsanfang für den angegebenen *Datumswert* mit dem Offset darstellt, falls angegeben.

**Beispiel**

```kusto
  range offset from -1 to 1 step 1
 | project monthStart = startofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|monthStart|
|---|
|2016-12-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-02-01 00:00:00.0000000|