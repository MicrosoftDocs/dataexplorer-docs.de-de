---
title: ROW_NUMBER ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird ROW_NUMBER () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 457e9445aa113e76052b9c4d96019352215d08f9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242801"
---
# <a name="row_number"></a>row_number()

Gibt den Index der aktuellen Zeile in einem [serialisierten Zeilen Satz](./windowsfunctions.md#serialized-row-set)zurück.
Der Zeilen Index beginnt standardmäßig bei `1` für die erste Zeile und wird `1` für jede weitere Zeile um inkrementiert.
Optional kann der Zeilen Index bei einem anderen Wert als gestartet werden `1` .
Außerdem kann der Zeilen Index gemäß dem bereitgestellten Prädikat zurückgesetzt werden.

## <a name="syntax"></a>Syntax

`row_number``(`[*StartingIndex* [ `,` *Restart*]]`)`

* *StartingIndex* ist ein konstanter Ausdruck vom Typ `long` , der den Wert des Zeilen Indexes angibt, bei dem begonnen wird (oder für den ein Neustart durchgestartet werden soll). Der Standardwert ist `1`.
* *Restart* ist ein optionales Argument vom Typ `bool` , das angibt, wann die Nummerierung mit dem *startingIndex* -Wert neu gestartet werden soll. Wenn kein Wert angegeben wird, wird der Standardwert von `false` verwendet.

## <a name="returns"></a>Rückgabe

Die-Funktion gibt den Zeilen Index der aktuellen Zeile als Wert des Typs zurück `long` .

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird eine Tabelle mit zwei Spalten zurückgegeben: die erste Spalte ( `a` ) mit Zahlen von bis `10` zu `1` und die zweite Spalte ( `rn` ) mit Zahlen von `1` bis zu `10` :

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number()
```

Das folgende Beispiel ähnelt dem obigen, nur die zweite Spalte ( `rn` ) beginnt bei `7` :

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number(7)
```

Im letzten Beispiel wird veranschaulicht, wie die Daten partitioniert und die Zeilen pro Partition partitioniert werden können. Hier werden die Daten partitioniert nach `Airport` :

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

Airport  | Luftfahrt  | Abschiebungen  | Rang
---------|----------|-------------|------
SEA      | BA       | 2           | 1
SEA      | LH       | 1           | 2
SEA      | LY       | 0           | 3
TLV      | LY       | 100         | 1
TLV      | LH       | 1           | 2