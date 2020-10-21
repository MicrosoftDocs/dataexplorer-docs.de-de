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
ms.openlocfilehash: 973ea6d5043db0f173fa0ebe98548b20968371b3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241168"
---
# <a name="startofyear"></a>startofyear()

Gibt den Anfang des Jahres zurück, der das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.

## <a name="syntax"></a>Syntax

`startofyear(`*Datum* [ `,` *Offset*]`)`

## <a name="arguments"></a>Argumente

* `date`: Das Eingabe Datum.
* `offset`: Eine optionale Anzahl von Offset Jahren ab dem Eingabe Datum (Integer, default-0). 

## <a name="returns"></a>Rückgabe

Ein DateTime-Wert, der den Anfang des Jahres für den angegebenen *Datums* Wert mit dem Offset darstellt, falls angegeben.

## <a name="example"></a>Beispiel

```kusto
  range offset from -1 to 1 step 1
 | project yearStart = startofyear(datetime(2017-01-01 10:10:17), offset) 
```

|yearstart|
|---|
|2016-01-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2018-01-01 00:00:00.0000000|