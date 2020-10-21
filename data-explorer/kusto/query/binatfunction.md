---
title: bin_at ()-Azure Daten-Explorer
description: In diesem Artikel wird bin_at () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ae888fc050387af28281b84229044114a72a5dbf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245383"
---
# <a name="bin_at"></a>bin_at()

Rundet Werte auf einen "bin" mit fester Größe, wobei die Steuerung über dem Ausgangspunkt der bin gesteuert wird.
(Siehe auch [`bin function`](./binfunction.md) .)

## <a name="syntax"></a>Syntax

`bin_at``(` *Ausdruck* `,` *binsize* `, ` *FixedPoint*`)`

## <a name="arguments"></a>Argumente

* *Ausdruck*: ein skalarer Ausdruck eines numerischen Typs (einschließlich `datetime` und `timespan` ), der den zu runden Wert angibt.
* *Binsize*: eine skalare Konstante eines numerischen Typs oder `timespan` (für einen- `datetime` Ausdruck oder einen- `timespan` *Ausdruck*), die die Größe der einzelnen bin angibt.
* *FixedPoint*: eine skalare Konstante desselben Typs wie der *Ausdruck* , der einen Wert des *Ausdrucks* angibt, der ein "fester Punkt" ist (d. h. ein Wert, `fixed_point` für den `bin_at(fixed_point, bin_size, fixed_point) == fixed_point` .)

## <a name="returns"></a>Rückgabe

Das nächste Vielfache von " *binsize* " unterhalb des *Ausdrucks*, der so verschoben wird, dass *FixedPoint* in sich selbst übersetzt wird.

## <a name="examples"></a>Beispiele

|expression                                                                    |Ergebnis                           |Kommentare                   |
|------------------------------------------------------------------------------|---------------------------------|---------------------------|
|`bin_at(6.5, 2.5, 7)`                                                         |`4.5`                            ||
|`bin_at(time(1h), 1d, 12h)`                                                   |`-12h`                           ||
|`bin_at(datetime(2017-05-15 10:20:00.0), 1d, datetime(1970-01-01 12:00:00.0))`|`datetime(2017-05-14 12:00:00.0)`|Alle Behälter werden um 12 Uhr mittags   |
|`bin_at(datetime(2017-05-17 10:20:00.0), 7d, datetime(2017-06-04 00:00:00.0))`|`datetime(2017-05-14 00:00:00.0)`|Alle Behälter werden sonntags angezeigt.|


Beachten Sie im folgenden Beispiel, dass der `"fixed point"` arg als einer der-Container zurückgegeben wird und die anderen Behälter basierend auf dem-Wert ausgerichtet werden `bin_size` . Beachten Sie außerdem, dass jeder DateTime-bin die Startzeit dieses bin darstellt:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto

datatable(Date:datetime, Num:int)[
datetime(2018-02-24T15:14),3,
datetime(2018-02-23T16:14),4,
datetime(2018-02-26T15:14),5]
| summarize sum(Num) by bin_at(Date, 1d, datetime(2018-02-24 15:14:00.0000000)) 
```

|Date|sum_Num|
|---|---|
|2018-02-23 15:14:00.0000000|4|
|2018-02-24 15:14:00.0000000|3|
|2018-02-26 15:14:00.0000000|5|
