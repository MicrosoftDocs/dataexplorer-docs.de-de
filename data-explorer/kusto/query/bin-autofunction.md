---
title: bin_auto ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird bin_auto () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dba71c3b9b52a4edaf3a9b1260f56fc94eb935e3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245428"
---
# <a name="bin_auto"></a>bin_auto()

Rundet Werte auf einen "bin" fester Größe, wobei die Kontrolle über die bin-Größe und den von einer Abfrage Eigenschaft bereitgestellten Startpunkt festgelegt wird.

## <a name="syntax"></a>Syntax

`bin_auto``(` *Ausdruck*`)`

## <a name="arguments"></a>Argumente

* *Ausdruck*: ein skalarer Ausdruck eines numerischen Typs, der den zu runden Wert angibt.

**Eigenschaften von Client Anforderungen**

* `query_bin_auto_size`: Ein numerisches Literale, das die Größe der einzelnen bin angibt.
* `query_bin_auto_at`: Ein numerisches Literale, das einen Wert des *Ausdrucks* angibt, der ein "fester Punkt" ist (d. h. ein Wert, `fixed_point` für den `bin_auto(fixed_point)` == `fixed_point` .)

## <a name="returns"></a>Rückgabe

Das nächstgelegene Vielfache des `query_bin_auto_at` folgenden *Ausdrucks*wurde verschoben, sodass es `query_bin_auto_at` in sich selbst übersetzt wird.

## <a name="examples"></a>Beispiele

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