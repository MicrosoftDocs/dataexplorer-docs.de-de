---
title: datetime_add ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird datetime_add () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ab395dadf178b296929300fe4cfd42742fba5f27
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247732"
---
# <a name="datetime_add"></a>datetime_add()

Berechnet einen neuen [DateTime](./scalar-data-types/datetime.md) -Wert aus einem angegebenen datepart-Wert multipliziert mit einem angegebenen Wert, der einem angegebenen [DateTime](./scalar-data-types/datetime.md)-Wert hinzugefügt wird.

## <a name="syntax"></a>Syntax

`datetime_add(`*Zeitraum* `,` *Betrag* `,` *DateTime*`)`

## <a name="arguments"></a>Argumente

* `period`: [Zeichenfolge](./scalar-data-types/string.md). 
* `amount`: [Integer](./scalar-data-types/int.md).
* `datetime`: [DateTime](./scalar-data-types/datetime.md) -Wert.

Mögliche Werte des *Zeitraums*: 
- Year
- Quarter
- Month (Monat)
- Woche
- Tag
- Hour
- Minute
- Second
- Millisekunde
- Mikrosekunde
- Nanosekunde

## <a name="returns"></a>Rückgabe

Es wurde ein Datum nach einem bestimmten Zeit-/datesintervall hinzugefügt.

## <a name="examples"></a>Beispiele

```kusto
print  year = datetime_add('year',1,make_datetime(2017,1,1)),
quarter = datetime_add('quarter',1,make_datetime(2017,1,1)),
month = datetime_add('month',1,make_datetime(2017,1,1)),
week = datetime_add('week',1,make_datetime(2017,1,1)),
day = datetime_add('day',1,make_datetime(2017,1,1)),
hour = datetime_add('hour',1,make_datetime(2017,1,1)),
minute = datetime_add('minute',1,make_datetime(2017,1,1)),
second = datetime_add('second',1,make_datetime(2017,1,1))

```

|Jahr|quarter|month|week|day|hour|minute|second|
|---|---|---|---|---|---|---|---|
|2018-01-01 00:00:00.0000000|2017-04-01 00:00:00.0000000|2017-02-01 00:00:00.0000000|2017-01-08 00:00:00.0000000|2017-01-02 00:00:00.0000000|2017-01-01 01:00:00.0000000|2017-01-01 00:01:00.0000000|2017-01-01 00:00:01.0000000|






