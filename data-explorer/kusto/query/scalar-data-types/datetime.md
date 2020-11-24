---
title: 'Der DateTime-Datentyp: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den DateTime-Datentyp in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: e65b16ac4a280f2ecbef2c4557cac4a8d7be13b3
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513130"
---
# <a name="the-datetime-data-type"></a>Der DateTime-Datentyp

Der `datetime` ( `date` )-Datentyp stellt einen Zeitpunkt dar, der normalerweise als Datum und Uhrzeit ausgedrückt wird.
Werte liegen im Bereich von 00:00:00 (Mitternacht), 1. Januar 0001, der den 1. Januar 0001 (Common Era) bis 11:59:59 Uhr, 31. Dezember 9999 n. Chr. unserer Zeitrechnung im gregorianischen Kalender. 

Die Zeitwerte werden in 100-Nanosecond-Einheiten, die als Ticks bezeichnet werden, und ein bestimmtes Datum die Anzahl der Ticks seit 12:00 Mitternacht, 1. Januar 0001, gemessen. unserer Zeitrechnung Im GregorianCalendar-Kalender (ohne Ticks, die durch Schaltsekunden hinzugefügt würden).
Ein Ticks-Wert von 31241376000000000 stellt z. b. das Datum, den Freitag, den 01. Januar 0100 12:00:00 Mitternacht dar.
Dies wird manchmal als "Zeit in linearer Zeit" bezeichnet.

> [!WARNING]
> Ein `datetime` Wert in Kusto liegt immer in der UTC-Zeitzone. Das Anzeigen von `datetime` Werten in anderen Zeitzonen liegt in der Verantwortung der Benutzeranwendung, die die Daten anzeigt, und nicht in einer Eigenschaft der Daten selbst. Wenn Zeit Zonen Werte als Teil der Daten aufbewahrt werden müssen, sollten separate Spalten verwendet werden (Bereitstellen von Offset Informationen in Bezug auf die UTC).

## <a name="datetime-literals"></a>datetime-Literale

Literale vom Typ `datetime` verfügen über den Syntax `datetime(` *Wert* `)` , bei dem eine Reihe von Formaten für den *Wert* unterstützt werden, wie in der folgenden Tabelle angegeben:

|Beispiel                                                     |Wert                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|Zeiten werden immer in UTC angegeben. Durch das Auslassen des Datums wird eine Zeit am heutigen Tag angegeben.|
|`datetime(null)`                                            |Siehe [NULL-Werte](null-values.md).                            |
|`now()`                                                     |Die aktuelle Zeit.                                             |
|`now(`-*TimeSpan*`)`                                        |`now()-`*TimeSpan*                                            |
|`ago(`*TimeSpan*`)`                                         |`now()-`*TimeSpan*                                            |

`now()` und `ago()` geben einen `datetime` Wert im Vergleich zu dem Zeitpunkt an, zu dem die Ausführung der Abfrage von Kusto gestartet wurde. Diese können mehrmals in derselben Abfrage vorkommen, und es wird jeweils ein einzelner Wert verwendet.
(Anders ausgedrückt: Ausdrücke wie werden `now(-x) - ago(x)` immer auf den `timespan` Wert 0 (null) ausgewertet.)

## <a name="supported-formats"></a>Unterstützte Formate

Es gibt mehrere Formate für `datetime` , die als [DateTime ()-Literale](#datetime-literals) und die [ToDateTime ()](../todatetimefunction.md) -Funktion unterstützt werden.

> [!WARNING]
> Es wird **dringend empfohlen** , nur die ISO 8601-Formate zu verwenden.

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|Format|Beispiel|
|------|-------|
|% Y-% m-% dt% h:%m:% s% z|2014-05-25t08:20:03.123456 z|
|% Y-% m-% dt% h:%m:% s|2014-05-25t08:20:03.123456|
|% Y-% m-% dt% h:%m|2014-05-25t08:20|
|% Y-% m-% d% h:%m:% s% z|2014-11-08 15:55:55.123456 z|
|% Y-% m-% d% h:%m:% s|2014-11-08 15:55:55|
|% Y-% m-% d% h:%m|2014-11-08 15:55|
|% Y-% m-% d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|Format|Beispiel|
|------|-------|
|% w,% e% b% r% h:%m:% s% Z|Sa, 8. Nov 14 15:05:02 GMT|
|% w,% e% b% r% h:%m:% s|Sa, 8. Nov 14 15:05:02|
|% w,% e% b% r% h:%m|Sa, 8. Nov 14 15:05|
|% w,% e% b% r% h:%m% Z|Sa, 8. Nov 14 15:05 GMT|
|% e% b% r% h:%m:% s% Z|8. Nov. 14 15:05:02 GMT|
|% e% b% r% h:%m:% s|8. Nov. 14 15:05:02|
|% e% b% r% h:%m|8. Nov. 14 15:05|
|% e% b% r% h:%m% Z|8. Nov. 14 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|Format|Beispiel|
|------|-------|
|% w,% e-% b-% r% h:%m:% s% Z|Samstag, 08-Nov-14 15:05:02 GMT|
|% w,% e-% b-% r% h:%m:% s|Samstag, 08-Nov-14 15:05:02|
|% w,% e-% b-% r% h:%m% Z|Samstag, 08-Nov-14 15:05 GMT|
|% w,% e-% b-% r% h:%m|Samstag, 08-Nov-14 15:05|
|% e-% b-% r% h:%m:% s% Z|08-Nov-14 15:05:02 GMT|
|% e-% b-% r% h:%m:% s|08-Nov-14 15:05:02|
|% e-% b-% r% h:%m% Z|08-Nov-14 15:05 GMT|
|% e-% b-% r% h:%m|08-Nov-14 15:05|


### <a name="sortable"></a>Sortierbar 

|Format|Beispiel|
|------|-------|        
|% Y-% n-% e% h:%m:% s|2014-11-08 15:05:25|
|% Y-% n-% e% h:%m:% s% Z|2014-11-08 15:05:25 GMT|
|% Y-% n-% e% h:%m|2014-11-08 15:05|
|% Y-% n-% e% h:%m% Z|2014-11-08 15:05 GMT|
|% Y-% n-% et% h:%m:% s|2014-11-08t15:05:25|
|% Y-% n-% et% h:%m:% s% Z|2014-11-08t15:05:25 GMT|
|% Y-% n-% et% h:%m|2014-11-08t15:05|
|% Y-% n-% et% h:%m% Z|2014-11-08t15:05 GMT|
