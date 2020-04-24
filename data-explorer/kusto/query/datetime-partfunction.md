---
title: datetime_part() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird datetime_part() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: c64208725f0d5c49a7ea7733f8eb5a208e19225b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516370"
---
# <a name="datetime_part"></a>datetime_part()

Extrahiert den angeforderten Datumsteil als Ganzzahlwert.

```kusto
datetime_part("Day",datetime(2015-12-14))
```

**Syntax**

`datetime_part(`*Teilzeit*`,`*datetime*`)`

**Argumente**

* `date`: `datetime`
* `part`: `string`

Mögliche Werte `part`von: 
- Jahr
- Quarter
- Monat
- week_of_year
- Day (Tag)
- Dayofyear
- Hour
- Minute
- Sekunde
- Millisekunde
- Mikrosekunde
- Nanosekunden

**Rückgabe**

Eine ganze Zahl, die das extrahierte Teil darstellt.

**Hinweis**

`week_of_year`gibt eine ganze Zahl zurück, die die Wochennummer darstellt. Die Wochennummer wird ab der ersten Woche eines Jahres berechnet, d. h. die Nummer, die den ersten Donnerstag enthält.

**Beispiele**

```kusto
let dt = datetime(2017-10-30 01:02:03.7654321); 
print 
year = datetime_part("year", dt),
quarter = datetime_part("quarter", dt),
month = datetime_part("month", dt),
weekOfYear = datetime_part("week_of_year", dt),
day = datetime_part("day", dt),
dayOfYear = datetime_part("dayOfYear", dt),
hour = datetime_part("hour", dt),
minute = datetime_part("minute", dt),
second = datetime_part("second", dt),
millisecond = datetime_part("millisecond", dt),
microsecond = datetime_part("microsecond", dt),
nanosecond = datetime_part("nanosecond", dt)

```

|year|quarter|month|weekOfYear|day|dayOfYear|hour|minute|second|Millisekunde|Mikrosekunde|Nanosekunde|
|---|---|---|---|---|---|---|---|---|---|---|---|
|2017|4|10|44|30|303|1|2|3|765|765432|765432100|

> [!NOTE]
> `weekofyear`ist eine veraltete Variante des Teils. `week_of_year` `weekofyear`war nicht ISO 8601-konform; die erste Woche eines Jahres wurde als die Woche mit dem ersten Mittwoch des Jahres definiert.
`week_of_year`ISO 8601-konform ist; die erste Woche eines Jahres wird als die Woche mit dem ersten Donnerstag des Jahres definiert. [Weitere Informationen](https://en.wikipedia.org/wiki/ISO_8601#Week_dates).