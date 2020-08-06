---
title: datetime_part ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird datetime_part () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: c786f0edc94a9b92ca0f4484d0d71166ee699883
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803980"
---
# <a name="datetime_part"></a>datetime_part()

Extrahiert den angeforderten Datums Teil als ganzzahligen Wert.

```kusto
datetime_part("Day",datetime(2015-12-14))
```

## <a name="syntax"></a>Syntax

`datetime_part(`*Teil* `,` *DateTime*`)`

## <a name="arguments"></a>Argumente

* `date`: `datetime`
* `part`: `string`

Mögliche Werte `part` : 
* Year
* Quarter
* Month
* week_of_year
* Tag
* DayOfYear
* Hour
* Minute
* Second
* Millisekunde
* Mikrosekunde
* Nanosekunde

## <a name="returns"></a>Gibt zurück

Eine ganze Zahl, die den extrahierten Teil darstellt.

> [!NOTE]
> `week_of_year`gibt eine ganze Zahl zurück, die die Wochen Nummer darstellt. Die Wochen Nummer wird von der ersten Woche eines Jahres berechnet, die den ersten Donnerstag enthält.

## <a name="examples"></a>Beispiele

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
> `weekofyear`ist eine veraltete Variante von `week_of_year` Part. `weekofyear`war nicht ISO 8601-kompatibel. die erste Woche eines Jahres wurde als Woche mit dem ersten Mittwoch des Jahrs definiert.
> `week_of_year`ist ISO 8601-kompatibel; die erste Woche eines Jahres wird als Woche mit dem ersten Donnerstag des Jahrs definiert. [Weitere Informationen](https://en.wikipedia.org/wiki/ISO_8601#Week_dates).