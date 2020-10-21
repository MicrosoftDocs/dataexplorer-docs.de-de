---
title: starto f Week ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird starto-Week () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5f53dfca4183c4dae623b41abf4c5bce6a3e828
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241191"
---
# <a name="startofweek"></a>startofweek()

Gibt den Anfang der Woche zurück, der das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.

Der Beginn der Woche gilt als Sonntag.

## <a name="syntax"></a>Syntax

`startofweek(`*Datum* [ `,` *Offset*]`)`

## <a name="arguments"></a>Argumente

* `date`: Das Eingabe Datum.
* `offset`: Eine optionale Anzahl von Offset Wochen ab dem Eingabe Datum (Integer, default-0).

## <a name="returns"></a>Rückgabe

Ein DateTime-Wert, der den Anfang der Woche für den angegebenen *Datums* Wert mit dem Offset darstellt, falls angegeben.

## <a name="example"></a>Beispiel

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|weekstart|
|---|
|2016-12-25 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-08 00:00:00.0000000|