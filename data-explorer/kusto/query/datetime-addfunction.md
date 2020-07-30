---
title: datetime_add ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird datetime_add () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 766f0617b70e21194d731ae1cf8eabf1014265bb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348546"
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

|year|quarter|month|week|day|hour|minute|second|
|---|---|---|---|---|---|---|---|
|2018-01-01 00:00:00.0000000|2017-04-01 00:00:00.0000000|2017-02-01 00:00:00.0000000|2017-01-08 00:00:00.0000000|2017-01-02 00:00:00.0000000|2017-01-01 01:00:00.0000000|2017-01-01 00:01:00.0000000|2017-01-01 00:00:01.0000000|






