---
title: make_datetime ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird make_datetime () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 09a199e907f8e09a845fa037129330a2b660187e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249831"
---
# <a name="make_datetime"></a>make_datetime()

Erstellt einen [DateTime](./scalar-data-types/datetime.md) -Skalarwert aus dem angegebenen Datum und der angegebenen Uhrzeit.

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

## <a name="syntax"></a>Syntax

`make_datetime(`*Jahr*,*Monat*,*Tag*`)`

`make_datetime(`*Jahr*,*Monat*,*Tag*,*Stunde*,*Minute*`)`

`make_datetime(`*Jahr*,*Monat*,*Tag*,*Stunde*,*Minute*,*Sekunde*`)`

## <a name="arguments"></a>Argumente

* *year*: Year (ein ganzzahliger Wert von 0 bis 9999)
* *Month*: month (ein ganzzahliger Wert von 1 bis 12)
* *Day*: Day (ein ganzzahliger Wert von 1 bis 28-31)
* *Hour*: Stunde (ein ganzzahliger Wert von 0 bis 23)
* *Minute*: Minute (ein ganzzahliger Wert zwischen 0 und 59)
* *Second*: Second (ein echter Wert, von 0 bis 59,9999999)

## <a name="returns"></a>Rückgabe

Wenn die Erstellung erfolgreich ist, ist das Ergebnis ein [DateTime](./scalar-data-types/datetime.md) -Wert. andernfalls ist das Ergebnis NULL.
 
## <a name="example"></a>Beispiel

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

