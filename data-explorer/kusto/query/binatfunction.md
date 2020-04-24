---
title: bin_at() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden bin_at() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 218142e1c377e6a72abde154d4576025698b2f0d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517441"
---
# <a name="bin_at"></a>bin_at()

Rundet Werte auf einen "Bin" fester Größe mit der Kontrolle über den Startpunkt des Lagerplatzes.
(Siehe [`bin function`](./binfunction.md)auch .)

**Syntax**

`bin_at``(` *Expression* Ausdruck`,` *BinSize* `, ` *FixedPoint*`)`

**Argumente**

* *Ausdruck*: Ein skalarer Ausdruck eines `datetime` `timespan`numerischen Typs (einschließlich und ), der den zu rundenden Wert angibt.
* *BinSize*: Eine Skalarkonstante desselben Typs wie *Ausdruck,* die die Größe jedes Abschnitts angibt. 
* *FixedPoint*: Eine Skalarkonstante desselben Typs wie *Ausdruck,* die einen Ausdruckswert angibt, *der* ein "fester Punkt" ist (d. h. ein Wert, `fixed_point` für den `bin_at(fixed_point, bin_size, fixed_point) == fixed_point`.)

**Rückgabe**

Das nächste Vielfache von *BinSize* unter *Ausdruck*verschoben, sodass *FixedPoint* in sich selbst übersetzt wird.

**Beispiele**

|Ausdruck                                                                    |Ergebnis                           |Kommentare                   |
|------------------------------------------------------------------------------|---------------------------------|---------------------------|
|`bin_at(6.5, 2.5, 7)`                                                         |`4.5`                            ||
|`bin_at(time(1h), 1d, 12h)`                                                   |`-12h`                           ||
|`bin_at(datetime(2017-05-15 10:20:00.0), 1d, datetime(1970-01-01 12:00:00.0))`|`datetime(2017-05-14 12:00:00.0)`|Alle Mülleimer werden mittags sein   |
|`bin_at(datetime(2017-05-17 10:20:00.0), 7d, datetime(2017-06-04 00:00:00.0))`|`datetime(2017-05-14 00:00:00.0)`|Alle Mülleimer werden sonntags sein|


Beachten Sie im folgenden `"fixed point"` Beispiel, dass der arg zurückgegeben wird, da einer der Abschnitte `bin_size`zurückgegeben wird und die anderen Abschnitte basierend auf dem an ihm ausgerichtet sind. Beachten Sie außerdem, dass jeder Datumszeitabschnitt die Startzeit dieses Lagerplatzes darstellt:

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