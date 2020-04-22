---
title: Skalare Datentypen – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden skalare Datentypen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 3ef87217beee62fe4cecf7ee95dfe8daba49af7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490241"
---
# <a name="scalar-data-types"></a>Skalare Datentypen

Jeder Datenwert (etwa der Wert eines Ausdrucks oder der Parameter einer Funktion) hat einen **Datentyp**. Bei Datentypen wird zwischen **skalaren Datentypen** (einer der integrierten vordefinierten Typen aus der Liste weiter unten) und **benutzerdefinierten Datensätzen** (eine geordnete Sequenz von Paaren aus Name und skalarem Datentyp – etwa der Datentyp einer Tabellenzeile) unterschieden.

Kusto bietet eine Reihe von Systemdatentypen, die alle Arten von Daten definieren, die mit Kusto verwendet werden können.

> [!NOTE]
> Benutzerdefinierte Datentypen werden in Kusto nicht unterstützt.

Die folgende Tabelle enthält neben den von Kusto unterstützten Datentypen auch zusätzliche Aliase, mit denen auf sie verwiesen werden kann, sowie einen vergleichbaren .NET Framework-Typ:

| type       | Weitere(r) Name(n)   | Entsprechender .NET-Typ              | gettype()   |Speichertyp (interner Name)|
| ---------- | -------------------- | --------------------------------- | ----------- |----------------------------|
| `bool`     | `boolean`            | `System.Boolean`                  | `int8`      |`I8`                        |
| `datetime` | `date`               | `System.DateTime`                 | `datetime`  |`DateTime`                  |
| `dynamic`  |                      | `System.Object`                   | `array`, `dictionary` oder einer der anderen Werte |`Dynamic`|
| `guid`     | `uuid`, `uniqueid`   | `System.Guid`                     | `guid`      |`UniqueId`                  |
| `int`      |                      | `System.Int32`                    | `int`       |`I32`                       |
| `long`     |                      | `System.Int64`                    | `long`      |`I64`                       |
| `real`     | `double`             | `System.Double`                   | `real`      |`R64`                       |
| `string`   |                      | `System.String`                   | `string`    |`StringBuffer`              |
| `timespan` | `time`               | `System.TimeSpan`                 | `timespan`  |`TimeSpan`                  |
| `decimal`  |                      | `System.Data.SqlTypes.SqlDecimal` | `decimal`   | `Decimal`                  |

Alle Datentypen enthalten einen speziellen NULL-Wert für fehlende oder nicht geeignete Daten. Der Versuch, die Zeichenfolge `"abc"` in einer Spalte vom Typ `int` zu erfassen, führt beispielsweise zu diesem Wert.
Dieser Wert kann nicht explizit materialisiert werden. Mithilfe der Funktion `isnull()` können Sie allerdings erkennen, ob ein Ausdruck diesen Wert ergibt.

> [!WARNING]
> Zum Zeitpunkt der Erstellung dieses Dokuments wird der Typ `guid` noch nicht vollständig unterstützt. Teams sollten daher stattdessen Werte vom Typ `string` verwenden.

