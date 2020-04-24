---
title: bin_auto() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird bin_auto() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ebb214ae6a2676bf59a37e1e4e9cc3c085374bb3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517832"
---
# <a name="bin_auto"></a>bin_auto()

Rundet Werte auf einen "bin" fester Größe ab, wobei die Kontrolle über die Lagerplatzgröße und den Startpunkt durch eine Abfrageeigenschaft bereitgestellt wird.

**Syntax**

`bin_auto` `(` *Ausdruck* `)`

**Argumente**

* *Ausdruck*: Ein skalarer Ausdruck eines numerischen Typs, der den zu rundenden Wert angibt.

**Clientanforderungseigenschaften**

* `query_bin_auto_size`: Ein numerisches Literal, das die Größe jedes Abschnitts angibt.
* `query_bin_auto_at`: Ein numerisches Literal, das einen Wert von *Expression* angibt, `fixed_point` der `bin_auto(fixed_point)` == `fixed_point`ein "fester Punkt" ist (d. h. ein Wert, für den .)

**Rückgabe**

Das nächste Vielfache `query_bin_auto_at` von unter *Ausdruck*verschoben, so dass in `query_bin_auto_at` sich selbst übersetzt werden.

**Beispiele**

```kusto
set query_bin_auto_size=1h;
set query_bin_auto_at=datetime(2017-01-01 00:05);
range Timestamp from datetime(2017-01-01 00:05) to datetime(2017-01-01 02:00) step 1m
| summarize count() by bin_auto(Timestamp)
```

|Timestamp                    | count_|
|-----------------------------|-------|
|2017-01-01 00:05:00.0000000  | 60    |
|2017-01-01 01:05:00.0000000  | 56    |