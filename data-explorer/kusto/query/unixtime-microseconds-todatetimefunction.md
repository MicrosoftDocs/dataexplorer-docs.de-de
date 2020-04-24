---
title: unixtime_microseconds_todatetime() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird unixtime_microseconds_todatetime() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 6ab593049fcafeba27b8158a1c9c6a5b6fa75fe3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505269"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

Konvertiert unix-epoch microseconds in UTC datetime.

**Syntax**

`unixtime_microseconds_todatetime(*microseconds*)`

**Argumente**

* *mikrosekunden*: Eine reelle Zahl stellt den Epochenzeitstempel in Mikrosekunden dar. `Datetime`die vor der Epoche (1970-01-01 00:00:00) auftritt, hat einen negativen Zeitstempelwert.

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein [datetime-Wert.](./scalar-data-types/datetime.md) Wenn die Konvertierung nicht erfolgreich ist, lautet das Ergebnis null.

**Siehe auch**

* Konvertieren Sie unix-epoch Sekunden in UTC datetime mit [unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md).
* Konvertieren Sie unix-epoch Millisekunden in UTC datetime mit [unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md).
* Konvertieren Sie unix-epoch nanoseconds in UTC datetime mit [unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md).

**Beispiel**

```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|Date_time|
|---|
|2019-01-01 00:00:00.0000000|