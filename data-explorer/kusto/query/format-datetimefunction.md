---
title: format_datetime ()-Azure Daten-Explorer
description: In diesem Artikel wird format_datetime () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: ec9fc6edf0e62c694e1090ea5d5adade333a80ac
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512926"
---
# <a name="format_datetime"></a>format_datetime()

Formatiert einen DateTime-Wert gemäß dem angegebenen Format.

```kusto
format_datetime(datetime(2015-12-14 02:03:04.12345), 'y-M-d h:m:s.fffffff') == "15-12-14 2:3:4.1234500"
```

## <a name="syntax"></a>Syntax

`format_datetime(`*DateTime* `,` *Format*`)`

## <a name="arguments"></a>Argumente

* `datetime`: Wert eines Typs `datetime` .
* `format`: die Format Bezeichner-Zeichenfolge, die aus einem oder mehreren [Format Elementen](#supported-formats)besteht.

## <a name="returns"></a>Gibt zurück

Die Zeichenfolge mit dem Format Ergebnis.

## <a name="supported-formats"></a>Unterstützte Formate

|Formatbezeichner   |BESCHREIBUNG    |Beispiele
|---|---|---
|`d`    |Der Tag des Monats, von 1 bis 31. | 2009-06-01t13:45:30-> 1, 2009-06-15t13:45:30-> 15
|`dd`   |Der Tag des Monats, von 01 bis 31.| 2009-06-01t13:45:30-> 01, 2009-06-15t13:45:30-> 15
|`f`    |Die Zehntelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.6170000-> 6, 2009-06-15t13:45:30.05-> 0
|`ff`   |Die Hundertstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.6170000-> 61, 2009-06-15t13:45:30.0050000-> 00
|`fff`  |Die Millisekunden in einem Datums- und Uhrzeitwert. |6/15/2009 13:45:30.617-> 617, 6/15/2009 13:45:30.0005-> 000
|`ffff` |Die Zehntausendstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.6175000-> 6175, 2009-06-15t13:45:30.0000500-> 0000
|`fffff`    |Die Hunderttausendstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.6175400-> 61754, 2009-06-15t13:45:30.000005-> 00000
|`ffffff`   |Die Millionstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.6175420-> 617542, 2009-06-15t13:45:30.0000005-> 000000
|`fffffff`  |Die Zehnmillionstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.6175425-> 6175425, 2009-06-15t13:45:30.0001150-> 0001150
|`F`    |Wenn ungleich 0 (null), die Zehntelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.6170000-> 6, 2009-06-15t13:45:30.0500000-> (keine Ausgabe)
|`FF`   |Wenn ungleich 0 (null), die Hundertstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.6170000-> 61, 2009-06-15t13:45:30.0050000-> (keine Ausgabe)
|`FFF`  |Wenn ungleich 0 (null), die Millisekunden in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.6170000-> 617, 2009-06-15t13:45:30.0005000-> (keine Ausgabe)
|`FFFF` |Wenn ungleich 0 (null), die Zehntausendstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.5275000-> 5275, 2009-06-15t13:45:30.0000500-> (keine Ausgabe)
|`FFFFF`    |Wenn ungleich 0 (null), die Hunderttausendstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.6175400-> 61754, 2009-06-15t13:45:30.0000050-> (keine Ausgabe)
|`FFFFFF`   |Wenn ungleich 0 (null), die Millionstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.6175420-> 617542, 2009-06-15t13:45:30.0000005-> (keine Ausgabe)
|`FFFFFFF`  |Wenn ungleich 0 (null), die Zehnmillionstelsekunde in einem Datums- und Uhrzeitwert. |2009-06-15t13:45:30.6175425-> 6175425, 2009-06-15t13:45:30.0001150-> 000115
|`h`    |Die Stunde, von 1 bis 12 (12-Stunden-Format). |2009-06-15t01:45:30-> 1, 2009-06-15t13:45:30-> 1
|`hh`   |Die Stunde, von 01 bis 12 (12-Stunden-Format). |2009-06-15t01:45:30-> 01, 2009-06-15t13:45:30-> 01
|`H`    |Die Stunde, von 0 bis 23 (24-Stunden-Format). |2009-06-15t01:45:30-> 1, 2009-06-15t13:45:30-> 13
|`HH`   |Die Stunde, von 00 bis 23 (24-Stunden-Format). |2009-06-15t01:45:30-> 01, 2009-06-15t13:45:30-> 13
|`m`    |Die Minute, von 0 bis 59. |2009-06-15t01:09:30-> 9, 2009-06-15t13:29:30-> 29
|`mm`   |Die Minute, von 00 bis 59. |2009-06-15t01:09:30-> 09, 2009-06-15t01:45:30-> 45
|`M`    |Der Monat, von 1 bis 12. |2009-06-15T13:45:30 -> 6
|`MM`   |Der Monat, von 01 bis 12.|2009-06-15T13:45:30 -> 06
|`s`    |Die Sekunde, von 0 bis 59. |2009-06-15T13:45:09 -> 9
|`ss`   |Die Sekunde, von 00 bis 59. |2009-06-15T13:45:09 -> 09
|`y`    |Das Jahr, von 0 bis 99. |0001-01-01t00:00:00-> 1, 0900-01-01t00:00:00-> 0, 1900-01-01t00:00:00-> 0, 2009-06-15t13:45:30-> 9, 2019-06-15t13:45:30-> 19
|`yy`   |Das Jahr, von 00 bis 99. | 0001-01-01t00:00:00-> 01, 0900-01-01t00:00:00-> 00, 1900-01-01t00:00:00-> 00, 2019-06-15t13:45:30-> 19
|`yyyy` |Das Jahr als vierstellige Zahl. | 0001-01-01t00:00:00-> 0001, 0900-01-01t00:00:00-> 0900, 1900-01-01t00:00:00-> 1900, 2009-06-15t13:45:30-> 2009
|`tt`   |AM/pm-Stunden |2009-06-15t13:45:09-> pm

**Unterstützte Trennzeichen**

Der Formatspezifizierer kann folgende Trennzeichen enthalten:

|Trennzeichen|Anmerkungen|
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

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let dt = datetime(2017-01-29 09:00:05);
print 
v1=format_datetime(dt,'yy-MM-dd [HH:mm:ss]'), 
v2=format_datetime(dt, 'yyyy-M-dd [H:mm:ss]'),
v3=format_datetime(dt, 'yy-MM-dd [hh:mm:ss tt]')
```

|v1|V2|V3|
|---|---|---|
|17-01-29 [09:00:05]|2017-1-29 [9:00:05]|17-01-29 [09:00:05 Uhr]|
