---
title: Skalare Datentypen – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden skalare Datentypen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: a7508866f85bb7edb5a6feee5cfe9d191b946a09
ms.sourcegitcommit: c09cc374d5d1d8b396c466ef397690b4b7e4174f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103481541"
---
# <a name="scalar-data-types"></a>Skalare Datentypen

Jeder Datenwert (etwa der Wert eines Ausdrucks oder der Parameter einer Funktion) hat einen **Datentyp**. Bei Datentypen wird zwischen **skalaren Datentypen** (einer der integrierten vordefinierten Typen aus der Liste weiter unten) und **benutzerdefinierten Datensätzen** (eine geordnete Sequenz von Paaren aus Name und skalarem Datentyp – etwa der Datentyp einer Tabellenzeile) unterschieden.

Kusto bietet eine Reihe von Systemdatentypen, die alle Arten von Daten definieren, die mit Kusto verwendet werden können.

> [!NOTE]
> Benutzerdefinierte Datentypen werden in Kusto nicht unterstützt.

Die folgende Tabelle enthält neben den von Kusto unterstützten Datentypen auch zusätzliche Aliase, mit denen auf sie verwiesen werden kann, sowie einen vergleichbaren .NET Framework-Typ:

| type       | Weitere(r) Name(n)   | Entsprechender .NET-Typ              | gettype()   |
| ---------- | -------------------- | --------------------------------- | ----------- |
| `bool`     | `boolean`            | `System.Boolean`                  | `int8`      |
| `datetime` | `date`               | `System.DateTime`                 | `datetime`  |
| `dynamic`  |                      | `System.Object`                   | `array`, `dictionary` oder einer der anderen Werte |
| `guid`     |                      | `System.Guid`                     | `guid`      |
| `int`      |                      | `System.Int32`                    | `int`       |
| `long`     |                      | `System.Int64`                    | `long`      |
| `real`     | `double`             | `System.Double`                   | `real`      |
| `string`   |                      | `System.String`                   | `string`    |
| `timespan` | `time`               | `System.TimeSpan`                 | `timespan`  |
| `decimal`  |                      | `System.Data.SqlTypes.SqlDecimal` | `decimal`   |

Alle Nicht-Zeichenfolgen-Datentypen enthalten einen speziellen NULL-Wert für fehlende oder nicht geeignete Daten. Der Versuch, die Zeichenfolge `"abc"` in einer Spalte vom Typ `int` zu erfassen, führt beispielsweise zu diesem Wert.
Dieser Wert kann nicht explizit materialisiert werden. Mithilfe der Funktion `isnull()` können Sie allerdings erkennen, ob ein Ausdruck diesen Wert ergibt.

> [!WARNING]
> Der Typ `guid` wird noch nicht vollständig unterstützt.
> Teams sollten daher stattdessen Werte vom Typ `string` verwenden.
