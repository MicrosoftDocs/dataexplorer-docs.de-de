---
title: 'Numerische Operatoren: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden numerische Operatoren in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b79979abdc13d5cb6b7a71bde2301ed0169ae59b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249759"
---
# <a name="numerical-operators"></a>Numerische Operatoren

Die Typen `int` , `long` und `real` stellen numerische Typen dar.
Die folgenden Operatoren können zwischen Paaren dieser Typen verwendet werden:

Operator       |BESCHREIBUNG                         |Beispiel
---------------|------------------------------------|-----------------------
`+`            |Add                                 |`3.14 + 3.14`, `ago(5m) + 5m`
`-`            |Subtrahieren                            |`0.23 - 0.22`,
`*`            |Multiplizieren                            |`1s * 5`, `2 * 2`
`/`            |Dividieren                              |`10m / 1s`, `4 / 2`
`%`            |Modulo                              |`4 % 2`
`<`            |Kleiner                                |`1 < 10`, `10sec < 1h`, `now() < datetime(2100-01-01)`
`>`            |Größer                             |`0.23 > 0.22`, `10min > 1sec`, `now() > ago(1d)`
`==`           |Ist gleich                              |`1 == 1`
`!=`           |Ungleich                          |`1 != 0`
`<=`           |Kleiner oder gleich                       |`4 <= 5`
`>=`           |Größer oder gleich                    |`5 >= 4`
`in`           |Entspricht einem der Elemente       |[siehe hier](inoperator.md)
`!in`          |Entspricht keinem der Elemente   |[siehe hier](inoperator.md)

**Kommentar zum Modulo-Operator**

Der Modulo von zwei Zahlen gibt immer in Kusto eine "kleine nicht negative Zahl" zurück.
Folglich ist der Modulo von zwei Zahlen ( *n*  %  *d*) so: 0 &le; (*N*  %  *d*) &lt; Abs (*d*).

Beispielsweise wird die folgende Abfrage:

```kusto
print plusPlus = 14 % 12, minusPlus = -14 % 12, plusMinus = 14 % -12, minusMinus = -14 % -12
```

Erzeugt dieses Ergebnis:

|plusplus  | minusplus  | plusminus  | minus minus|
|----------|------------|------------|-----------|
|2         | 10         | 2          | 10        |