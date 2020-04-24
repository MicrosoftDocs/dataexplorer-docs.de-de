---
title: Datums- / Zeitspannenarithmetik - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Datums-/Zeitzeitarithmetik in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 34bda7b8418dd519995f25c625a5031202a64a8b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516319"
---
# <a name="datetime--timespan-arithmetic"></a>Datetime / Zeitspanarithmetik

Kusto unterstützt die Durchführung arithmetischer `datetime` `timespan`Operationen für Werte von Typen und:

* Man kann zwei `datetime` Werte subtrahieren (aber nicht hinzufügen), um einen `timespan` Wert zu erhalten, der ihre Differenz ausdrückt.
  So alt `datetime(1997-06-25) - datetime(1910-06-11)` war zum Beispiel [Jacques-Yves Cousteau,](https://en.wikipedia.org/wiki/Jacques_Cousteau) als er starb.

* Man kann zwei `timespan` Werte hinzufügen oder `timespan` subtrahieren, um einen Wert zu erhalten, der ihre Summe oder Differenz ist.
  Zum Beispiel `1d + 2d` sind drei Tage.

* Man kann einen `timespan` Wert von `datetime` einem Wert hinzufügen oder subtrahieren.
  Ist z. B. das Datum, `datetime(1910-06-11) + 1d` an dem Cousteau einen Tag alt geworden ist.

* Man kann `timespan` zwei Werte teilen, um ihren Quotienten zu erhalten.
  Gibt `1d / 5h` z. `4.8`B. .
  Dies gibt einem die `timespan` Möglichkeit, einen `timespan` beliebigen Wert als Vielfaches eines anderen Werts auszudrücken. Um z. B. eine Stunde `1h` in `1s` `1h / 1s` Sekunden auszudrücken, teilen `3600`Sie einfach durch : (mit dem offensichtlichen Ergebnis, ).

* Umgekehrt kann man einen numerischen Wert `double` `long`(z. `timespan` B. `timespan` und ) durch einen Wert vervielfachen, um einen Wert abzubekommen.
  Zum Beispiel kann man eineinhalb Stunden `1.5 * 1h`als ausdrücken.

## <a name="example-unix-time"></a>Beispiel: Unix-Zeit

[Unix-Zeit](https://en.wikipedia.org/wiki/Unix_time) (auch bekannt als POSIX-Zeit oder UNIX-Epoch-Zeit) ist ein System zur Beschreibung eines Zeitpunkts als die Anzahl der Sekunden, die seit 00:00:00 Donnerstag, 1. Januar 1970, Koordinierte Weltzeit (UTC), minus Schaltsekunden verstrichen sind.

Wenn Ihre Daten die Darstellung der Unix-Zeit als ganzzahlige Zeit enthalten oder Sie konvertieren müssen, stehen die folgenden Funktionen zur Verfügung:

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
> Zusätzlich zu den oben genannten Funktionen siehe dedizierte Funktionen für Unix-Epochen-Zeitkonvertierungen: [unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md)
> [unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md)
> [unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md)
> [unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)