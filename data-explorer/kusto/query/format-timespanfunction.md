---
title: format_timespan() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird format_timespan() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2cc894d3b10cd3e4badd1ff7b498c23618c76818
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514993"
---
# <a name="format_timespan"></a>format_timespan()

Formatiert eine Zeitspanne entsprechend dem angegebenen Format.

```kusto
format_timespan(time(14.02:03:04.12345), 'h:m:s.fffffff') == "2:3:4.1234500"
```

**Syntax**

`format_timespan(`*Timespan-Format* `,` *format*`)`

**Argumente**

* `timespan`: Wert eines `timespan`Typs .
* `format`: Formatbezeichnerzeichenfolge, bestehend aus einem oder mehreren [Formatelementen](#supported-formats).

**Rückgabe**

Die Zeichenfolge mit dem Formatergebnis.

## <a name="supported-formats"></a>Unterstützte Formate

|Formatbezeichner   |Beschreibung    |Beispiele
|---|---|---
|`d`-`dddddddd` |Die Anzahl ganzer Tage im Zeitintervall. Bei Bedarf mit Nullen gepolstert.|   15.13:45:30: d -> 15, dd -> 15, ddd -> 015
|`f`    |Die Zehntelsekunden im Zeitintervall. |15.13:45:30.6170000 -> 6, 15.13:45:30.05 -> 0
|`ff`   |Die Hundertstelsekunden im Zeitintervall. |15.13:45:30.6170000 -> 61, 15.13:45:30.0050000 -> 00
|`fff`  |Die Millisekunden im Zeitintervall. |15.06.2009 13:45:30.617 -> 617, 15.06.2009 13:45:30.0005 -> 000
|`ffff` |Die Zehntausendstelsekunden im Zeitintervall. |15.13:45:30.6175000 -> 6175, 15.13:45:30.0000500 -> 0000
|`fffff`    |Die Hunderttausendstelsekunden im Zeitintervall. |15.13:45:30.6175400 -> 61754, 15.13:45:30.000005 -> 00000
|`ffffff`   |Die Millionstelsekunden im Zeitintervall. |15.13:45:30.6175420 -> 617542, 15.13:45:30.0000005 -> 000000
|`fffffff`  |Die zehn MillionstelSekunden im Zeitintervall. |15.13:45:30.6175425 -> 6175425, 15.13:45:30.0001150 -> 0001150
|`F`    |Wenn nicht Null, die Zehntelsekunden im Zeitintervall. |15.13:45:30.6170000 -> 6, 15.13:45:30.0500000 -> (keine Ausgabe)
|`FF`   |Wenn nicht Null, die Hundertstelsekunde im Zeitintervall. |15.13:45:30.6170000 -> 61, 15.13:45:30.0050000 -> (keine Ausgabe)
|`FFF`  |Wenn ungleich Null, die Millisekunden im Zeitintervall. |15.13:45:30.6170000 -> 617, 15.13:45:30.0005000 -> (keine Ausgabe)
|`FFFF` |Wenn nicht Null, die zehn Tausendstelsekunde im Zeitintervall. |15.13:45:30.5275000 -> 5275, 15.13:45:30.0000500 -> (keine Ausgabe)
|`FFFFF`    |Wenn nicht Null, die Tausendstelsekunde im Zeitintervall. |15.13:45:30.6175400 -> 61754, 15.13:45:30.0000050 -> (keine Ausgabe)
|`FFFFFF`   |Wenn nicht Null, die MillionstelSekunde im Zeitintervall. |15.13:45:30.6175420 -> 617542, 15.13:45:30.0000005 -> (keine Ausgabe)
|`FFFFFFF`  |Wenn nicht Null, die zehn Millionstel Sekunden im Zeitintervall. |15.13:45:30.6175425 -> 6175425, 15.13:45:30.0001150 -> 000115
|`h`    |Die Anzahl ganzer Stunden im Zeitintervall, die nicht als Teil von Tagen gezählt werden. Einstellige Stundenangaben weisen keine führende 0 auf. |15.01:45:30 -> 1, 15.13:45:30 -> 1
|`hh`   |Die Anzahl ganzer Stunden im Zeitintervall, die nicht als Teil von Tagen gezählt werden. Einstellige Stundenangaben weisen eine führende 0 auf. |15.01:45:30 -> 01, 15.13:45:30 -> 01
|`H`    |Die Stunde, von 0 bis 23 (24-Stunden-Format). |15.01:45:30 -> 1, 15.13:45:30 -> 13
|`HH`   |Die Stunde, von 00 bis 23 (24-Stunden-Format). |15.01:45:30 -> 01, 15.13:45:30 -> 13
|`m`    |Die Anzahl ganzer Minuten im Zeitintervall, die nicht als Teil von Stunden oder Tagen gezählt werden. Einstellige Minutenangaben weisen keine führende 0 auf. |15.01:09:30 -> 9, 15.13:29:30 -> 29
|`mm`   |Die Anzahl ganzer Minuten im Zeitintervall, die nicht als Teil von Stunden oder Tagen gezählt werden. Einstellige Minutenangaben weisen eine führende 0 auf. |15.01:09:30 -> 09, 15.01:45:30 -> 45
|`s`    |Die Anzahl ganzer Sekunden im Zeitintervall, die nicht als Teil von Stunden, Tagen oder Minuten gezählt werden. Einstellige Sekundenangaben weisen keine führende 0 auf. |15.13:45:09 -> 9
|`ss`   |Die Anzahl ganzer Sekunden im Zeitintervall, die nicht als Teil von Stunden, Tagen oder Minuten gezählt werden. Einstellige Sekundenangaben weisen eine führende 0 auf. |15.13:45:09 -> 09

**Unterstützte Delimeter**

Formatbezeichner kann folgende Delimeter-Zeichen enthalten:

|Trennzeichenn|Comment|
|---------|-------|
|`' '`| LeerZchn|
|`'/'`||
|`'-'`|Strich|
|`':'`||
|`','`||
|`'.'`||
|`'_'`||
|`'['`||
|`']'`||

**Beispiele**

```kusto
let t = time(29.09:00:05.12345);
print 
v1=format_timespan(t, 'dd.hh:mm:ss:FF'),
v2=format_timespan(t, 'ddd.h:mm:ss [fffffff]')
```

|v1|V2|
|---|---|
|29.09:00:05:12|029.9:00:05 [1234500]|
