---
title: Numerische Operatoren - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden numerische Operatoren in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6667005960152814be952d93fe932b4971d5322b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512018"
---
# <a name="numerical-operators"></a>Numerische Operatoren

Die `int`Typen `long`, `real` und stellen numerische Typen dar.
Die folgenden Operatoren können zwischen Paaren dieser Typen verwendet werden:

Operator       |BESCHREIBUNG                         |Beispiel
---------------|------------------------------------|-----------------------
`+`            |Hinzufügen                                 |`3.14 + 3.14`, `ago(5m) + 5m`
`-`            |Subtrahieren                            |`0.23 - 0.22`,
`*`            |Multiplizieren                            |`1s * 5`, `2 * 2`
`/`            |Dividieren                              |`10m / 1s`, `4 / 2`
`%`            |Modulo                              |`4 % 2`
`<`            |Kleiner                                |`1 < 10`, `10sec < 1h`, `now() < datetime(2100-01-01)`
`>`            |Größer                             |`0.23 > 0.22`, `10min > 1sec`, `now() > ago(1d)`
`==`           |Equals                              |`1 == 1`
`!=`           |Not Equals                          |`1 != 0`
`<=`           |Kleiner als oder gleich                       |`4 <= 5`
`>=`           |Größer als oder gleich                    |`5 >= 4`
`in`           |Entspricht einem der Elemente       |[siehe hier](inoperator.md)
`!in`          |Entspricht keinem der Elemente   |[siehe hier](inoperator.md)

**Kommentar zum Modulo-Operator**

Das Modulo zweier Zahlen gibt in Kusto immer eine "kleine nicht-negative Zahl" zurück.
So ist das Modulo von zwei Zahlen, *N* % *D*, so, dass: &le; 0 (*N* % *D*) &lt; abs(*D*).

Beispielsweise wird die folgende Abfrage:

```kusto
print plusPlus = 14 % 12, minusPlus = -14 % 12, plusMinus = 14 % -12, minusMinus = -14 % -12
```

Erzeugt dieses Ergebnis:

|plusPlus  | minusPlus  | plusMinus  | minusMinus|
|----------|------------|------------|-----------|
|2         | 10         | 2          | 10        |