---
title: datetime_diff() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird datetime_diff() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fd62e27ac4f9ef0ec813a311ddb2b16f0a6c9a65
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516438"
---
# <a name="datetime_diff"></a>datetime_diff()

Berechnet die Kalenderdifferenz zwischen zwei [Datumszeitwerten.](./scalar-data-types/datetime.md)

**Syntax**

`datetime_diff(`*Zeitraum*`,`*datetime_1*`,`*datetime_2*`)`

**Argumente**

* `period`: `string`. 
* `datetime_1`: [Datetime-Wert.](./scalar-data-types/datetime.md)
* `datetime_2`: [Datetime-Wert.](./scalar-data-types/datetime.md)

Mögliche *Periodenwerte*: 
- Jahr
- Quarter
- Monat
- Week
- Day (Tag)
- Hour
- Minute
- Sekunde
- Millisekunde
- Mikrosekunde
- Nanosekunden

**Rückgabe**

Eine ganze Zahl, die `periods` die Menge des Subtraktionsergebnisses (`datetime_1` - `datetime_2`) darstellt.

**Beispiele**

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



