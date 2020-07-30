---
title: unixtime_seconds_todatetime ()-Azure Daten-Explorer
description: In diesem Artikel wird unixtime_seconds_todatetime () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 0dca68d410bc7444feca8df41a360bb4ac2a1ec3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338642"
---
# <a name="unixtime_seconds_todatetime"></a>unixtime_seconds_todatetime()

Konvertiert Unix-Epoch seconds in UTC-DateTime.

## <a name="syntax"></a>Syntax

`unixtime_seconds_todatetime(*seconds*)`

## <a name="arguments"></a>Argumente

* *seconds*: eine reelle Zahl stellt einen Epochen Zeitstempel in Sekunden dar. `Datetime`Dies tritt auf, bevor die Epochen Zeit (1970-01-01 00:00:00) einen negativen Zeitstempelwert aufweist.

## <a name="returns"></a>Gibt zurück

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein [DateTime](./scalar-data-types/datetime.md) -Wert. Wenn die Konvertierung nicht erfolgreich ist, ist das Ergebnis NULL.

**Weitere Informationen**

* Konvertieren Sie UNIX-Epoch-Millisekunden in UTC-DateTime mithilfe von [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md).
* Konvertieren Sie UNIX-Epoch-Mikrosekunden in UTC-DateTime mithilfe von [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md).
* Konvertieren von UNIX-Epochen (Nanosekunden) in UTC-DateTime mithilfe von [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md).

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_seconds_todatetime(1546300800)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
