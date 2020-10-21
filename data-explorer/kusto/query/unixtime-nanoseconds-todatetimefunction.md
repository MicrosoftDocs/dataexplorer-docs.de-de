---
title: unixtime_nanoseconds_todatetime ()-Azure Daten-Explorer
description: In diesem Artikel wird unixtime_nanoseconds_todatetime () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: ca933f8d7ae61cbb0f49cf1e2226ffe51ba7bff7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241157"
---
# <a name="unixtime_nanoseconds_todatetime"></a>unixtime_nanoseconds_todatetime()

Konvertiert Unix-Epoch Nanosekunden in UTC-DateTime.

## <a name="syntax"></a>Syntax

`unixtime_nanoseconds_todatetime(*nanoseconds*)`

## <a name="arguments"></a>Argumente

* *Nanosekunden*: eine reelle Zahl stellt einen Epochen Zeitstempel in Nanosekunden dar. `Datetime` Dies tritt auf, bevor die Epochen Zeit (1970-01-01 00:00:00) einen negativen Zeitstempelwert aufweist.

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein [DateTime](./scalar-data-types/datetime.md) -Wert. Wenn die Konvertierung nicht erfolgreich ist, ist das Ergebnis NULL.

## <a name="see-also"></a>Weitere Informationen

* Konvertieren Sie UNIX-Epoch seconds in UTC-DateTime mithilfe von [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md).
* Konvertieren Sie UNIX-Epoch-Millisekunden in UTC-DateTime mithilfe von [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md).
* Konvertieren Sie UNIX-Epoch-Mikrosekunden in UTC-DateTime mithilfe von [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md).

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_nanoseconds_todatetime(1546300800000000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
