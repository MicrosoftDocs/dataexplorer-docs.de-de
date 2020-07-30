---
title: EndOf Week ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird EndOf Week () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 57fa1764753e730f9ff0a2b01a70e0c221217d23
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348274"
---
# <a name="endofweek"></a>endofweek()

Gibt das Ende der Woche zurück, das das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.

Der letzte Tag der Woche gilt als Samstag.

## <a name="syntax"></a>Syntax

`endofweek(`*Datum* [ `,` *Offset*]`)`

## <a name="arguments"></a>Argumente

* `date`: Das Eingabe Datum.
* `offset`: Eine optionale Anzahl von Offset Wochen ab dem Eingabe Datum (Integer, default-0).

## <a name="returns"></a>Rückgabe

Ein DateTime-Wert, der das Ende der Woche für den angegebenen *Datums* Wert mit dem Offset darstellt, falls angegeben.

## <a name="example"></a>Beispiel

```kusto
  range offset from -1 to 1 step 1
 | project weekEnd = endofweek(datetime(2017-01-01 10:10:17), offset)  

```

|Wochen|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-07 23:59:59.9999999|
|2017-01-14 23:59:59.9999999|