---
title: hash_combine ()-Azure Daten-Explorer
description: In diesem Artikel wird hash_combine () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 91a178ded007ff75d96ed73f864276aa7d193d8c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244768"
---
# <a name="hash_combine"></a>hash_combine()

Kombiniert Hashwerte von zwei oder mehr Hashes.

## <a name="syntax"></a>Syntax

`hash_combine(`*H1* `,` *H2* [ `,` *H3* ...]`)`

## <a name="arguments"></a>Argumente

* *H1*: Long-Wert, der den ersten Hashwert darstellt.
* *H2*: Long-Wert, der den zweiten Hashwert darstellt.
* *HN*: Long-Wert, der den Nth-Hashwert darstellt.

## <a name="returns"></a>Rückgabe

Der kombinierte Hashwert der angegebenen skalare.

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|ersten|Halbjahr|erter|
|---|---|---|---|---|
|Hallo|World|753694413698530628|1846988464401551951|-1440138333540407281|
