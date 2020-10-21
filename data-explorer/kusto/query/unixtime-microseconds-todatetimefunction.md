---
title: unixtime_microseconds_todatetime ()-Azure Daten-Explorer
description: In diesem Artikel wird unixtime_microseconds_todatetime () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 713cf936878d0fa311d4637f61881f0481229617
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245702"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

Konvertiert Unix-Epoche-Mikrosekunden in UTC-DateTime.

## <a name="syntax"></a>Syntax

`unixtime_microseconds_todatetime(*microseconds*)`

## <a name="arguments"></a>Argumente

* *Mikrosekunden*: eine reelle Zahl stellt einen Epochen Zeitstempel in Mikrosekunden dar. `Datetime` Dies tritt auf, bevor die Epochen Zeit (1970-01-01 00:00:00) einen negativen Zeitstempelwert aufweist.

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein [DateTime](./scalar-data-types/datetime.md) -Wert. Wenn die Konvertierung nicht erfolgreich ist, ist das Ergebnis NULL.

## <a name="see-also"></a>Weitere Informationen

* Konvertieren Sie UNIX-Epoch seconds in UTC-DateTime mithilfe von [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md).
* Konvertieren Sie UNIX-Epoch-Millisekunden in UTC-DateTime mithilfe von [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md).
* Konvertieren von UNIX-Epochen (Nanosekunden) in UTC-DateTime mithilfe von [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md).

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
