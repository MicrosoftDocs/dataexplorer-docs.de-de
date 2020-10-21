---
title: EndOf month ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt endosmonth () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1911546f95b86a04fe59d22137b1eaeff22e2c2a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245145"
---
# <a name="endofmonth"></a>endofmonth()

Gibt das Ende des Monats zurück, das das Datum enthält, das durch einen Offset verschoben wird, falls angegeben.

## <a name="syntax"></a>Syntax

`endofmonth(`*Datum* [ `,` *Offset*]`)`

## <a name="arguments"></a>Argumente

* `date`: Das Eingabe Datum.
* `offset`: Eine optionale Anzahl von Offset Monaten ab dem Eingabe Datum (Integer, default-0).

## <a name="returns"></a>Rückgabe

Ein DateTime-Wert, der das Ende des Monats für den angegebenen *Datums* Wert mit dem Offset darstellt, falls angegeben.

## <a name="example"></a>Beispiel

```kusto
  range offset from -1 to 1 step 1
 | project monthEnd = endofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|monthend|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-31 23:59:59.9999999|
|2017-02-28 23:59:59.9999999|