---
title: EndOf Day ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird endosday () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a9d5da550a22bfb773a5b706baddff7365f80b74
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348308"
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