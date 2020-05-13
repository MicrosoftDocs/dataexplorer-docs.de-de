---
title: unixtime_nanoseconds_todatetime ()-Azure Daten-Explorer
description: In diesem Artikel wird unixtime_nanoseconds_todatetime () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 6c56d588ec9635b8225161ffdd47dc90eabdca3d
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370497"
---
# <a name="unixtime_nanoseconds_todatetime"></a>unixtime_nanoseconds_todatetime()

Konvertiert Unix-Epoch Nanosekunden in UTC-DateTime.

**Syntax**

`unixtime_nanoseconds_todatetime(*nanoseconds*)`

**Argumente**

* *Nanosekunden*: eine reelle Zahl stellt einen Epochen Zeitstempel in Nanosekunden dar. `Datetime`Dies tritt auf, bevor die Epochen Zeit (1970-01-01 00:00:00) einen negativen Zeitstempelwert aufweist.

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein [DateTime](./scalar-data-types/datetime.md) -Wert. Wenn die Konvertierung nicht erfolgreich ist, ist das Ergebnis NULL.

**Siehe auch**

* Konvertieren Sie UNIX-Epoch seconds in UTC-DateTime mithilfe von [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md).
* Konvertieren Sie UNIX-Epoch-Millisekunden in UTC-DateTime mithilfe von [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md).
* Konvertieren Sie UNIX-Epoch-Mikrosekunden in UTC-DateTime mithilfe von [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md).

**Beispiel**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_nanoseconds_todatetime(1546300800000000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
