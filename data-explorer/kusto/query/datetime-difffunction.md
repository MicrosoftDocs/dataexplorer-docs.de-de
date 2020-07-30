---
title: datetime_diff ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird datetime_diff () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2e116661610e343c90276a43421d263bf74cd1b5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348529"
---
# <a name="datetime_diff"></a>datetime_diff()

Berechnet die calendarian-Differenz zwischen zwei [DateTime](./scalar-data-types/datetime.md) -Werten.

## <a name="syntax"></a>Syntax

`datetime_diff(`*Zeitraum* `,` *datetime_1* `,` *datetime_2*`)`

## <a name="arguments"></a>Argumente

* `period`: `string`. 
* `datetime_1`: [DateTime](./scalar-data-types/datetime.md) -Wert.
* `datetime_2`: [DateTime](./scalar-data-types/datetime.md) -Wert.

Mögliche Werte des *Zeitraums*: 
- Year
- Quarter
- Month
- Week
- Tag
- Hour
- Minute
- Second
- Millisekunde
- Mikrosekunde
- Nanosekunde

## <a name="returns"></a>Rückgabe

Eine ganze Zahl, die die Anzahl von `periods` im Ergebnis der Subtraktion ( `datetime_1`  -  `datetime_2` ) darstellt.

## <a name="examples"></a>Beispiele

```kusto
print
year = datetime_diff('year',datetime(2017-01-01),datetime(2000-12-31)),
quarter = datetime_diff('quarter',datetime(2017-07-01),datetime(2017-03-30)),
month = datetime_diff('month',datetime(2017-01-01),datetime(2015-12-30)),
week = datetime_diff('week',datetime(2017-10-29 00:00),datetime(2017-09-30 23:59)),
day = datetime_diff('day',datetime(2017-10-29 00:00),datetime(2017-09-30 23:59)),
hour = datetime_diff('hour',datetime(2017-10-31 01:00),datetime(2017-10-30 23:59)),
minute = datetime_diff('minute',datetime(2017-10-30 23:05:01),datetime(2017-10-30 23:00:59)),
second = datetime_diff('second',datetime(2017-10-30 23:00:10.100),datetime(2017-10-30 23:00:00.900)),
millisecond = datetime_diff('millisecond',datetime(2017-10-30 23:00:00.200100),datetime(2017-10-30 23:00:00.100900)),
microsecond = datetime_diff('microsecond',datetime(2017-10-30 23:00:00.1009001),datetime(2017-10-30 23:00:00.1008009)),
nanosecond = datetime_diff('nanosecond',datetime(2017-10-30 23:00:00.0000000),datetime(2017-10-30 23:00:00.0000007))
```

|year|quarter|month|week|day|hour|minute|second|Millisekunde|Mikrosekunde|Nanosekunde|
|---|---|---|---|---|---|---|---|---|---|---|
|17|2|13|5|29|2|5|10|100|100|-700|



