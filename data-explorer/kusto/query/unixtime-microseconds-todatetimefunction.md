---
title: unixtime_microseconds_todatetime ()-Azure Daten-Explorer
description: In diesem Artikel wird unixtime_microseconds_todatetime () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 377fea9906c6078bc4c4ff685e2d06c670f2cafb
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103481"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

Konvertiert Unix-Epoche-Mikrosekunden in UTC-DateTime.

## <a name="syntax"></a>Syntax

`unixtime_microseconds_todatetime(*microseconds*)`

## <a name="arguments"></a>Argumente

* *Mikrosekunden*: eine reelle Zahl stellt einen Epochen Zeitstempel in Mikrosekunden dar. `Datetime` Dies tritt auf, bevor die Epochen Zeit (1970-01-01 00:00:00) einen negativen Zeitstempelwert aufweist.

## <a name="returns"></a>Gibt zurück

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
