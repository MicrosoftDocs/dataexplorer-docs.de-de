---
title: make_datetime() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird make_datetime() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c51b99e4d668ec4a5f96cdfd72442d7bcab553a5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512919"
---
# <a name="make_datetime"></a>make_datetime()

Erstellt einen [Datumsskalawert](./scalar-data-types/datetime.md) für Datumszeiten aus dem angegebenen Datum und der angegebenen Uhrzeit.

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

**Syntax**

`make_datetime(`*Jahr*,*Monat*,*Tag*`)`

`make_datetime(`*Jahr*,*Monat*,*Tag*,*Stunde*,*Minute*`)`

`make_datetime(`*Jahr*,*Monat*,*Tag*,*Stunde*,*Minute*,*Sekunde*`)`

**Argumente**

* *Jahr*: Jahr (ganzzahliger Wert, von 0 bis 9999)
* *Monat*: Monat (ganzzahliger Wert, von 1 bis 12)
* *Tag*: Tag (ein ganzzahliger Wert, von 1 bis 28-31)
* *Stunde*: Stunde (ein Ganzzahlwert, von 0 bis 23)
* *Minute*: Minute (ein Ganzzahlwert, von 0 bis 59)
* *Sekunde*: Sekunde (ein realer Wert, von 0 bis 59,9999999)

**Rückgabe**

Wenn die Erstellung erfolgreich ist, ist das Ergebnis ein [datetime-Wert,](./scalar-data-types/datetime.md) andernfalls ist das Ergebnis null.
 
**Beispiel**

```kusto
print year_month_day = make_datetime(2017,10,01)
```

|year_month_day|
|---|
|2017-10-01 00:00:00.0000000|




```kusto
print year_month_day_hour_minute = make_datetime(2017,10,01,12,10)
```

|year_month_day_hour_minute|
|---|
|2017-10-01 12:10:00.0000000|




```kusto
print year_month_day_hour_minute_second = make_datetime(2017,10,01,12,11,0.1234567)
```

|year_month_day_hour_minute_second|
|---|
|2017-10-01 12:11:00.1234567|

