---
title: EndOf Day ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird endosday () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d87f188cf0ae3639df4261d1813ebbe2a6788192
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245077"
---
# <a name="endofday"></a>endofday()

Gibt das Ende des Tages zurück, das das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.

## <a name="syntax"></a>Syntax

`endofday(`*Datum* [ `,` *Offset*]`)`

## <a name="arguments"></a>Argumente

* `date`: Das Eingabe Datum.
* `offset`: Eine optionale Anzahl von Offset Tagen ab dem Eingabe Datum (Integer, default-0).

## <a name="returns"></a>Rückgabe

Ein DateTime-Wert, der das Endtag für den angegebenen *Datums* Wert mit dem Offset darstellt, falls angegeben.

## <a name="example"></a>Beispiel

```kusto
  range offset from -1 to 1 step 1
 | project dayEnd = endofday(datetime(2017-01-01 10:10:17), offset) 
```

|tagend|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-01 23:59:59.9999999|
|2017-01-02 23:59:59.9999999|