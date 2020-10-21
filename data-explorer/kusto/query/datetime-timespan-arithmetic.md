---
title: 'DateTime/TimeSpan-Arithmetik: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die DateTime-/TimeSpan-Arithmetik in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 0b70cb1730d893e07790ade3079be21da209648c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247684"
---
# <a name="datetime--timespan-arithmetic"></a>DateTime/TimeSpan-Arithmetik

Kusto unterstützt das Ausführen von arithmetischen Vorgängen für Werte von Typen `datetime` und `timespan` :

* Sie können zwei Werte subtrahieren (aber nicht hinzufügen) `datetime` , um einen Wert zu erhalten, `timespan` der Ihren Unterschied ausdrückt.
  Beispielsweise `datetime(1997-06-25) - datetime(1910-06-11)` ist der alte [Jacques-Yves Cousteau](https://en.wikipedia.org/wiki/Jacques_Cousteau) , als er gestorben war.

* Sie können zwei Werte hinzufügen oder subtrahieren `timespan` , um einen Wert zu erhalten `timespan` , der Ihre Summe oder Differenz ist.
  Beispielsweise `1d + 2d` ist drei Tage.

* Ein Wert kann einem Wert hinzugefügt oder subtrahiert werden `timespan` `datetime` .
  Beispielsweise `datetime(1910-06-11) + 1d` ist das Datum, an dem Cousteau ein Tag alt ist.

* Sie können zwei `timespan` Werte aufteilen, um den Quotienten zu erhalten.
  `1d / 5h`Gibt z. b `4.8` . an.
  Dadurch haben Sie die Möglichkeit, beliebige `timespan` Werte als ein Vielfaches eines anderen `timespan` Werts auszudrücken. Um z. b. eine Stunde in Sekunden auszudrücken, teilen Sie einfach `1h` durch `1s` : `1h / 1s` (mit dem offensichtlichsten Ergebnis `3600` ).

* Umgekehrt können Sie einen numerischen Wert (z. b. `double` und `long` ) durch einen Wert über einen Wert `timespan` erhalten, um einen Wert zu erhalten `timespan` .
  Beispielsweise kann eine Stunde und eine Hälfte als ausgedrückt werden `1.5 * 1h` .

## <a name="example-unix-time"></a>Beispiel: Unix-Zeit

Die [UNIX-Zeit](https://en.wikipedia.org/wiki/Unix_time) (auch als POSIX-Zeit oder UNIX-Epochen Zeit bezeichnet) ist ein System zum Beschreiben eines Zeitpunkts als Anzahl von Sekunden, die seit 00:00:00 Donnerstag, 1. Januar 1970, UTC (koordinierte Weltzeit), minus Schaltsekunden verstrichen sind.

Wenn Ihre Daten eine Darstellung der Unix-Zeit als ganze Zahl enthalten, oder Sie eine solche in diese schreiben müssen, sind die folgenden Funktionen verfügbar:

```kusto
let fromUnixTime = (t:long)
{ 
    datetime(1970-01-01) + t * 1sec 
};
print result = fromUnixTime(1546897531)
```

|result                     |
|---------------------------|
|2019-01-07 21:45:31.0000000|

```kusto
let toUnixTime = (dt:datetime) 
{ 
    (dt - datetime(1970-01-01)) / 1s 
};
print result = toUnixTime(datetime(2019-01-07 21:45:31.0000000))
```

|result                     |
|---------------------------|
|1546897531                 |

> [!NOTE]
> Zusätzlich zu den oben aufgeführten Funktionen finden Sie weitere Informationen unter dedizierte Funktionen für UNIX-Epoch-Zeit Konvertierungen: [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md) 
>  [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md) 
>  [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md) 
>  [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md)