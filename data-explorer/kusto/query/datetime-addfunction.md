---
title: datetime_add() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden datetime_add() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ead7e0ae5c4dee94930afe1b20c4d5b99e2b4664
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516455"
---
# <a name="datetime_add"></a>datetime_add()

Berechnet eine neue [Datumsuhr](./scalar-data-types/datetime.md) aus einem angegebenen Datumsteil multipliziert mit einem angegebenen Betrag, der zu einem angegebenen [Datumszeitpunkt](./scalar-data-types/datetime.md)hinzugefügt wird.

**Syntax**

`datetime_add(`*Periodenbetrag*`,`*amount*`,`*datetime*`)`

**Argumente**

* `period`: [Zeichenfolge](./scalar-data-types/string.md). 
* `amount`: [ganzzahlig](./scalar-data-types/int.md).
* `datetime`: [Datetime-Wert.](./scalar-data-types/datetime.md)

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

Ein Datum nach dem Hinzugefügt eines bestimmten Zeit-/Datumsintervalls.

**Beispiele**

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






