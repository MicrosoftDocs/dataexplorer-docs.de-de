---
title: pack_array ()-Azure Daten-Explorer
description: In diesem Artikel wird pack_array () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3e9fbe7c11aeffbdc0274d4433eddd9a5463b262
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248683"
---
# <a name="pack_array"></a>pack_array()

Packt alle Eingabewerte in ein dynamisches Array.

## <a name="syntax"></a>Syntax

`pack_array(`*Expr1* `[` , ` *Expr2*]` ) '

## <a name="arguments"></a>Argumente

* *Expr1... N*: Eingabe Ausdrücke, die in ein dynamisches Array gepackt werden sollen.

## <a name="returns"></a>Rückgabe

Dynamisches Array, das die Werte von expr1, expr2,..., exprN enthält.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

|Column1|
|---|
|[1, 2, 4]|
|[2, 4, 8]|
|[3, 6, 12]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = tostring(x * 2)
| extend z = (x * 2) * 1s
| project pack_array(x,y,z)
```

|Column1|
|---|
|[1, "2", "00:00:02"]|
|[2, "4", "00:00:04"]|
|[3, "6", "00:00:06"]|
