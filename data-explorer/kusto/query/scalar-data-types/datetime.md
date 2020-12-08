---
title: Der Datentyp „datetime“ – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird der Datentyp „datetime“ in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: e65b16ac4a280f2ecbef2c4557cac4a8d7be13b3
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513130"
---
# <a name="the-datetime-data-type"></a>Der Datentyp „datetime“

Der Datentyp `datetime` (`date`) stellt einen Zeitpunkt dar, der üblicherweise als Datum und Uhrzeit eines Tages ausgedrückt wird.
Der Wertebereich erstreckt sich von 00:00:00 (Mitternacht) am 1. Januar 0001 bis 23:59:59 am 31. Dezember 9999 unserer Zeitrechnung im gregorianischen Kalender. 

Zeitwerte werden in 100-Nanosekunden-Einheiten gemessen, die als Ticks bezeichnet werden, und ein bestimmtes Datum ergibt sich aus der Anzahl der Ticks seit 00:00 am 1. Januar 0001 unserer Zeitrechnung im GregorianCalendar-Kalender (ohne Ticks, die wegen Schaltsekunden hinzugefügt würden).
Der Tickwert „31241376000000000“ entspricht beispielsweise dem Datum Freitag, 01. Januar 0100, 00:00:00 (Mitternacht).
Dies wird manchmal als „ein Moment in linearer Zeit“ bezeichnet.

> [!WARNING]
> Ein `datetime`-Wert in Kusto gilt immer für die UTC-Zeitzone. Ein Anzeigen von `datetime`-Werten in anderen Zeitzonen liegt in der Verantwortung der Benutzeranwendung, die die Daten anzeigt, und ist keine Eigenschaft der Daten selbst. Müssen Zeitzonenwerte als Teil der Daten aufbewahrt werden, sollte eine separate Spalte verwendet werden (in der Offsetinformationen relativ zu UTC bereitgestellt werden).

## <a name="datetime-literals"></a>datetime-Literale

Literale des Typs `datetime` haben die Syntax `datetime(`*Wert*`)`. Dabei wird für *Wert* eine Reihe von Formaten unterstützt, wie der folgenden Tabelle zu entnehmen ist:

|Beispiel                                                     |Wert                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|Zeiten werden immer in UTC angegeben. Durch das Auslassen des Datums wird eine Zeit am heutigen Tag angegeben.|
|`datetime(null)`                                            |Weitere Informationen finden Sie unter [NULL-Werte](null-values.md).                            |
|`now()`                                                     |Die aktuelle Zeit.                                             |
|`now(`-*timespan*`)`                                        |`now()-`*timespan*                                            |
|`ago(`*timespan*`)`                                         |`now()-`*timespan*                                            |

`now()` und `ago()` geben einen `datetime`-Wert bezogen auf den Zeitpunkt an, zu dem Kusto das Ausführen der Abfrage gestartet hat. Diese können mehrmals in derselben Abfrage vorkommen, und es wird jeweils ein einzelner Wert für alle verwendet.
(Anders ausgedrückt: Ausdrücke wie `now(-x) - ago(x)` werden immer zu einem `timespan`-Wert von 0 (null) ausgewertet.)

## <a name="supported-formats"></a>Unterstützte Formate

Es gibt mehrere Formate für `datetime`, die als [datetime()-Literale](#datetime-literals) und die [todatetime()](../todatetimefunction.md)-Funktion unterstützt werden.

> [!WARNING]
> Es wird **dringend empfohlen**, nur die ISO 8601-Formate zu verwenden.

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|Format|Beispiel|
|------|-------|
|%Y-%m-%dT%H:%M:%s%z|2014-05-25T08:20:03.123456Z|
|%Y-%m-%dT%H:%M:%s|2014-05-25T08:20:03.123456|
|%Y-%m-%dT%H:%M|2014-05-25T08:20|
|%Y-%m-%d %H:%M:%s%z|2014-11-08 15:55:55.123456Z|
|%Y-%m-%d %H:%M:%s|2014-11-08 15:55:55|
|%Y-%m-%d %H:%M|2014-11-08 15:55|
|%Y-%m-%d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|Format|Beispiel|
|------|-------|
|%w, %e %b %r %H:%M:%s %Z|Sat, 8 Nov 14 15:05:02 GMT|
|%w, %e %b %r %H:%M:%s|Sat, 8 Nov 14 15:05:02|
|%w, %e %b %r %H:%M|Sat, 8 Nov 14 15:05|
|%w, %e %b %r %H:%M %Z|Sat, 8 Nov 14 15:05 GMT|
|%e %b %r %H:%M:%s %Z|8 Nov 14 15:05:02 GMT|
|%e %b %r %H:%M:%s|8 Nov 14 15:05:02|
|%e %b %r %H:%M|8 Nov 14 15:05|
|%e %b %r %H:%M %Z|8 Nov 14 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|Format|Beispiel|
|------|-------|
|%w, %e-%b-%r %H:%M:%s %Z|Samstag, 08-Nov-14 15:05:02 GMT|
|%w, %e-%b-%r %H:%M:%s|Samstag, 08-Nov-14 15:05:02|
|%w, %e-%b-%r %H:%M %Z|Samstag, 08-Nov-14 15:05 GMT|
|%w, %e-%b-%r %H:%M|Samstag, 08-Nov-14 15:05|
|%e-%b-%r %H:%M:%s %Z|08-Nov-14 15:05:02 GMT|
|%e-%b-%r %H:%M:%s|08-Nov-14 15:05:02|
|%e-%b-%r %H:%M %Z|08-Nov-14 15:05 GMT|
|%e-%b-%r %H:%M|08-Nov-14 15:05|


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
|%Y-%n-%eT%H:%M %Z|2014-11-08T15:05 GMT|
