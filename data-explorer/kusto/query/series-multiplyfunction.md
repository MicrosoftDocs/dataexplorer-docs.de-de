---
title: series_multiply ()-Azure Daten-Explorer
description: In diesem Artikel wird series_multiply () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 21095174a9b2f25a4c48fdd65c05579ed7efff7d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246079"
---
# <a name="series_multiply"></a>series_multiply()

Berechnet die Element Weise Multiplikation von zwei numerischen Reihen Eingaben.

## <a name="syntax"></a>Syntax

`series_multiply(`*Series1* `,` *series2*`)`

## <a name="arguments"></a>Argumente

* *Series1, series2*: Eingabe numerische Arrays, die Element Weise mit einem dynamischen Array Ergebnis multipliziert werden sollen. Alle Argumente müssen dynamische Arrays sein. 

## <a name="returns"></a>Rückgabe

Dynamisches Array der berechneten Element weisen Multiplikations Operation zwischen den beiden Eingaben. Ein nicht numerisches Element oder ein nicht vorhandenes Element (Arrays mit unterschiedlichen Größen) ergeben einen- `null` Elementwert.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_multiply_s2 = series_multiply(s1, s2)
```

|s1         |s2|        s1_multiply_s2|
|---|---|---|
|[1, 2, 4]    |[4, 2, 1]|   [4, 4, 4]|
|[2, 4, 8]    |[8, 4, 2]|   [16, 16, 16]|
|[3, 6, 12]   |[12, 6, 3]|  [36, 36, 36]|
