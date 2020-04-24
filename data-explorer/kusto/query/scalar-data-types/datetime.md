---
title: Der Datetime-Datentyp - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Datentyp datetime in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a5a234c49a5bb5b04ccde49e7fb3702d927e38b5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509910"
---
# <a name="the-datetime-data-type"></a>Der Datetime-Datentyp

Der `datetime` `date`Datentyp ( ) stellt einen Zeiteingang dar, der in der Regel als Datum und Uhrzeit ausgedrückt wird.
Die Werte reichen von 00:00:00 (Mitternacht), 1. Januar 0001 Anno Domini (Common Era) bis 23:59:59 P.M., 31. Dezember 9999 N. (C.E.) im Gregorianischen Kalender. 

Zeitwerte werden in 100-Nanosekunden-Einheiten gemessen, die als Ticks bezeichnet werden, und ein bestimmtes Datum ist die Anzahl der Ticks seit 12:00 Mitternacht, 1. Januar 0001 n. Chr. (C.E.) im GregorianCalendar-Kalender (ohne Ticks, die durch Schaltsekunden hinzugefügt werden).
Beispielsweise stellt ein Tick-Wert von 31241376000000000 das Datum, Freitag, 01. Januar, 0100 12:00:00 Mitternacht dar.
Dies wird manchmal als "ein Moment in linearer Zeit" bezeichnet.

> [!WARNING]
> Ein `datetime` Wert in Kusto befindet sich immer in der UTC-Zeitzone. Das `datetime` Anzeigen von Werten in anderen Zeitzonen liegt in der Verantwortung der Benutzeranwendung, die die Daten anzeigt, nicht in der Eigenschaft der Daten selbst. Sollten Zeitzonenwerte als Teil der Daten beibehalten werden müssen, sollten separate Spalten verwendet werden (die Offsetinformationen relativ zu UTC bereitstellen).

## <a name="datetime-literals"></a>datetime-Literale

Literale vom `datetime` Typ `datetime(`haben den *Syntaxwert*`)`, wobei eine Reihe von Formaten für *den Wert*unterstützt werden, wie in der folgenden Tabelle angegeben:

|Beispiel                                                     |Wert                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|Zeiten werden immer in UTC angegeben. Durch das Auslassen des Datums wird eine Zeit am heutigen Tag angegeben.|
|`datetime(null)`                                            |Siehe [NULL-Werte](null-values.md).                            |
|`now()`                                                     |Die aktuelle Zeit.                                             |
|`now(`-*Timespan*`)`                                        |`now()-`*Timespan*                                            |
|`ago(`*Timespan*`)`                                         |`now()-`*Timespan*                                            |

`now()`und `ago()` geben `datetime` Sie einen Wert im Vergleich zu dem Zeitpunkt an, zu dem Kusto mit der Ausführung der Abfrage begonnen hat. Diese können mehrmals in derselben Abfrage angezeigt werden, und für alle wird ein einzelner Wert verwendet.
(Mit anderen Worten, `now(-x) - ago(x)` Ausdrücke, `timespan` wie sie immer auf den Wert Null ausgewertet werden.)

## <a name="supported-formats"></a>Unterstützte Formate

Es gibt mehrere `datetime` Formate, die als [datetime()-Literale](#datetime-literals) und die [todatetime()-Funktion](../todatetimefunction.md) unterstützt werden.

> [!WARNING]
> Es wird **dringend empfohlen,** nur die ISO 8601-Formate zu verwenden.

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|Format|Beispiel|
|------|-------|
|%Y-%m-%dT%H:%M:%s%z|25.05.2014 08:20:03.123456Z|
|%Y-%m-%dT%H:%M:%s"|25.05.2014 08:20:03.123456|
|%Y-%m-%dT%H:%M"|25.05.2014 08:20 Uhr|
|%Y-%m-%d %H:%M:%s%z"|2014-11-08 15:55:55.123456Z|
|%Y-%m-%d %H:%M:%s"|2014-11-08 15:55:55|
|%Y-%m-%d %H:%M"|2014-11-08 15:55|
|%Y-%m-%d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|Format|Beispiel|
|------|-------|
|%w, %e %b %r %H:%M:%s %Z|Sa, 8 Nov 14 15:05:02 GMT|
|%w, %e %b %r %H:%M:%s|Sa, 8 Nov 14 15:05:02|
|%w, %e %b %r %H:%M|Sa, 8 Nov 14 15:05|
|%w, %e %b %r %H:%M %Z|Sa, 8 Nov 14 15:05 GMT|
|%e %b %r %H:%M:%s %Z|8 Nov 14 15:05:02 GMT|
|%e %b %r %H:%M:%s"|8 Nov 14 15:05:02|
|%e %b %r %H:%M"|8. Nov 14 15:05|
|%e %b %r %H:%M %Z"|8 Nov 14 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|Format|Beispiel|
|------|-------|
|%w, %e-%b-%r %H:%M:%s %Z|Samstag, 08-Nov-14 15:05:02 GMT|
|%w, %e-%b-%r %H:%M:%s|Samstag, 08-Nov-14 15:05:02|
|%w, %e-%b-%r %H:%M %Z|Samstag, 08-Nov-14 15:05 GMT|
|%w, %e-%b-%r %H:%M|Samstag, 08-Nov-14 15:05|
|%e-%b-%r %H:%M:%s %Z|08-Nov-14 15:05:02 GMT|
|%e-%b-%r %H:%M:%s"|08-Nov-14 15:05:02|
|%e-%b-%r %H:%M %Z"|08-Nov-14 15:05 GMT|
|%e-%b-%r %H:%M"|08-Nov-14 15:05|


### <a name="sortable"></a>Sortierbar 

|Format|Beispiel|
|------|-------|        
|%Y-%n-%e %H:%M:%s|2014-11-08 15:05:25|
|%Y-%n-%e %H:%M:%s %Z|2014-11-08 15:05:25 GMT|
|%Y-%n-%e %H:%M|2014-11-08 15:05|
|%Y-%n-%e %H:%M %Z|2014-11-08 15:05 GMT|
|%Y-%n-%eT%H:%M:%s|2014-11-08T15:05:25|
|%Y-%n-%eT%H:%M:%s %Z|2014-11-08T15:05:25 GMT|
|%Y-%n-%eT%H:%M|2014-11-08T15:05|
|%Y-%n-%eT%H:%M %Z"|2014-11-08T15:05 GMT|