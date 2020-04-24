---
title: row_number() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird row_number() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8cb01ed098d24632154215ddf06dc2ab1d72695
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510165"
---
# <a name="row_number"></a>row_number()

Gibt den Index der aktuellen Zeile in einem [serialisierten Zeilensatz](./windowsfunctions.md#serialized-row-set)zurück.
Der Zeilenindex beginnt `1` standardmäßig bei der ersten Zeile `1` und wird für jede weitere Zeile um erhöht.
Optional kann der Zeilenindex mit einem `1`anderen Wert als beginnen.
Darüber hinaus kann der Zeilenindex entsprechend einem bereitgestellten Prädikat zurückgesetzt werden.

**Syntax**

`row_number``(` [*StartingIndex* [`,` *Neustart*]]`)`

* *StartingIndex* ist ein konstanter Ausdruck des Typs, `long` der den Wert des Zeilenindex angibt, mit dem er beginnen (oder neu gestartet werden soll). Standardwert: `1`.
* *Restart* ist ein optionales Argument vom Typ, `bool` das angibt, wann die Nummerierung auf den *StartingIndex-Wert* neu gestartet werden soll. Wenn nicht angegeben, wird `false` der Standardwert von verwendet.

**Rückgabe**

Die Funktion gibt den Zeilenindex der aktuellen `long`Zeile als Wert des Typs zurück.

**Beispiele**

Im folgenden Beispiel wird eine Tabelle mit`a`zwei Spalten `10` zurückgegeben, `1`die erste Spalte`rn`( ) `1` mit `10`Zahlen von unten nach und die zweite Spalte ( ) mit Zahlen von bis zu:

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number()
```

Das folgende Beispiel ähnelt dem obigen,`rn`nur die `7`zweite Spalte ( ) beginnt bei :

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number(7)
```

Das letzte Beispiel zeigt, wie man die Daten partitionieren und die Zeilen pro Partition nummerieren kann. Hier partitionieren wir `Airport`die Daten durch:

```kusto
datatable (Airport:string, Airline:string, Departures:long)
[
  "TLV", "LH", 1,
  "TLV", "LY", 100,
  "SEA", "LH", 1,
  "SEA", "BA", 2,
  "SEA", "LY", 0
]
| sort by Airport asc, Departures desc
| extend Rank=row_number(1, prev(Airport) != Airport)
```

Das Ausführen dieser Abfrage führt zu folgendem Ergebnis:

Flughafen  | Luftfahrt  | Abfahrten  | Rank
---------|----------|-------------|------
SEA      | BA       | 2           | 1
SEA      | Lh       | 1           | 2
SEA      | LY       | 0           | 3
Tlv      | LY       | 100         | 1
Tlv      | Lh       | 1           | 2