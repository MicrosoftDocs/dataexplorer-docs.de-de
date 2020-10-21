---
title: starto Mode ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird starto Mode () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c7e13cf809174fd258f9e7245bb2e537e2408c28
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241743"
---
# <a name="startofmonth"></a>startofmonth()

Gibt den Anfang des Monats zurück, der das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.

## <a name="syntax"></a>Syntax

`startofmonth(`*Datum* [ `,` *Offset*]`)`

## <a name="arguments"></a>Argumente

* `date`: Das Eingabe Datum.
* `offset`: Eine optionale Anzahl von Offset Monaten ab dem Eingabe Datum (Integer, default-0).

## <a name="returns"></a>Rückgabe

Ein DateTime-Wert, der den Anfang des Monats für den angegebenen *Datums* Wert mit dem Offset darstellt, falls angegeben.

## <a name="example"></a>Beispiel

```kusto
  range offset from -1 to 1 step 1
 | project monthStart = startofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|monthstart|
|---|
|2016-12-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-02-01 00:00:00.0000000|