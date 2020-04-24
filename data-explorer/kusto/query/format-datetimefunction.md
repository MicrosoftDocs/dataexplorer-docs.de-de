---
title: format_datetime() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden format_datetime() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c3a3502b4a9d4c9425c3dd5fe1f24050370524a4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515044"
---
# <a name="format_datetime"></a>format_datetime()

Formatiert eine Datumsuhr nach dem angegebenen Format.

```kusto
format_datetime(datetime(2015-12-14 02:03:04.12345), 'y-M-d h:m:s.fffffff') == "15-12-14 2:3:4.1234500"
```

**Syntax**

`format_datetime(`*Datetime-Format* `,` *format*`)`

**Argumente**

* `datetime`: Wert eines `datetime`Typs .
* `format`: Formatbezeichnerzeichenfolge, bestehend aus einem oder mehreren [Formatelementen](#supported-formats).

**Rückgabe**

Die Zeichenfolge mit dem Formatergebnis.

## <a name="supported-formats"></a>Unterstützte Formate

|Formatbezeichner   |Beschreibung    |Beispiele
|---|---|---
|`d`    |Der Tag des Monats, von 1 bis 31. | 2009-06-01T13:45:30 -> 1, 2009-06-15T13:45:30 -> 15
|`dd`   |Der Tag des Monats, von 01 bis 31.| 2009-06-01T13:45:30 -> 01, 2009-06-15T13:45:30 -> 15
|`f`    |Die Zehntelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.6170000 -> 6, 2009-06-15T13:45:30.05 -> 0
|`ff`   |Die Hundertstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.6170000 -> 61, 2009-06-15T13:45:30.0050000 -> 00
|`fff`  |Die Millisekunden in einem Datums- und Uhrzeitwert. |15.06.2009 13:45:30.617 -> 617, 15.06.2009 13:45:30.0005 -> 000
|`ffff` |Die Zehntausendstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.6175000 -> 6175, 2009-06-15T13:45:30.0000500 -> 0000
|`fffff`    |Die Hunderttausendstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.6175400 -> 61754, 2009-06-15T13:45:30.000005 -> 00000
|`ffffff`   |Die Millionstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.6175420 -> 617542, 2009-06-15T13:45:30.0000005 -> 0000000
|`fffffff`  |Die Zehnmillionstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.6175425 -> 6175425, 2009-06-15T13:45:30.0001150 -> 0001150
|`F`    |Wenn ungleich 0 (null), die Zehntelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.6170000 -> 6, 2009-06-15T13:45:30.0500000 -> (keine Ausgabe)
|`FF`   |Wenn ungleich 0 (null), die Hundertstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.6170000 -> 61, 2009-06-15T13:45:30.0050000 -> (kein Ausgang)
|`FFF`  |Wenn ungleich 0 (null), die Millisekunden in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.6170000 -> 617, 2009-06-15T13:45:30.0005000 -> (keine Ausgabe)
|`FFFF` |Wenn ungleich 0 (null), die Zehntausendstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.5275000 -> 5275, 2009-06-15T13:45:30.0000500 -> (keine Ausgabe)
|`FFFFF`    |Wenn ungleich 0 (null), die Hunderttausendstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.6175400 -> 61754, 2009-06-15T13:45:30.0000050 -> (keine Ausgabe)
|`FFFFFF`   |Wenn ungleich 0 (null), die Millionstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.6175420 -> 617542, 2009-06-15T13:45:30.0000005 -> (kein Ausgang)
|`FFFFFFF`  |Wenn ungleich 0 (null), die Zehnmillionstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15T13:45:30.6175425 -> 6175425, 2009-06-15T13:45:30.0001150 -> 000115
|`h`    |Die Stunde, von 1 bis 12 (12-Stunden-Format). |2009-06-15T01:45:30 -> 1, 2009-06-15T13:45:30 -> 1
|`hh`   |Die Stunde, von 01 bis 12 (12-Stunden-Format). |2009-06-15T01:45:30 -> 01, 2009-06-15T13:45:30 -> 01
|`H`    |Die Stunde, von 0 bis 23 (24-Stunden-Format). |2009-06-15T01:45:30 -> 1, 2009-06-15T13:45:30 -> 13
|`HH`   |Die Stunde, von 00 bis 23 (24-Stunden-Format). |2009-06-15T01:45:30 -> 01, 2009-06-15T13:45:30 -> 13
|`m`    |Die Minute, von 0 bis 59. |2009-06-15T01:09:30 -> 9, 2009-06-15T13:29:30 -> 29
|`mm`   |Die Minute, von 00 bis 59. |2009-06-15T01:09:30 -> 09, 2009-06-15T01:45:30 -> 45
|`M`    |Der Monat, von 1 bis 12. |2009-06-15T13:45:30 -> 6
|`MM`   |Der Monat, von 01 bis 12.|2009-06-15T13:45:30 -> 06
|`s`    |Die Sekunde, von 0 bis 59. |2009-06-15T13:45:09 -> 9
|`ss`   |Die Sekunde, von 00 bis 59. |2009-06-15T13:45:09 -> 09
|`y`    |Das Jahr, von 0 bis 99. |0001-01-01T00:00:00 -> 1, 0900-01-01T00:00:00 -> 0, 1900-01-01T00:00:00 -> 0, 2009-06-15T13:45:30 -> 9, 2019-06-15T13:45:30 -> 19 19
|`yy`   |Das Jahr, von 00 bis 99. | 0001-01-01T00:00:00 -> 01, 0900-01-01T00:00:00 -> 00, 1900-01-01T00:00:00 -> 00, 2019-06-15T13:45:30 -> 19
|`yyyy` |Das Jahr als vierstellige Zahl. | 0001-01-01T00:00:00 -> 0001, 0900-01-01T00:00:00 -> 0900, 1900-01-01T00:00:00 -> 1900, 2009-06-15T13:45:30 -> 2009
|`tt`   |AM / PM Stunden |15.06.2009 -> 15.06.200913:45:09 Uhr

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
let dt = datetime(2017-01-29 09:00:05);
print 
v1=format_datetime(dt,'yy-MM-dd [HH:mm:ss]'), 
v2=format_datetime(dt, 'yyyy-M-dd [H:mm:ss]'),
v3=format_datetime(dt, 'yy-MM-dd [hh:mm:ss tt]')
```

|v1|V2|V3|
|---|---|---|
|17-01-29 [09:00:05]|2017-1-29 [9:00:05]|17-01-29 [09:00:05]|