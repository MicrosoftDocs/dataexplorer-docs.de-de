---
title: hash_combine ()-Azure Daten-Explorer
description: In diesem Artikel wird hash_combine () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 1925d9b27382dd3a888e14243bfecad51d37db0d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226702"
---
# <a name="hash_combine"></a>hash_combine()

Kombiniert Hashwerte von zwei oder mehr Hashes.

**Syntax**

`hash_combine(`*H1* `,` *H2* [ `,` *H3* ...]`)`

**Argumente**

* *H1*: Long-Wert, der den ersten Hashwert darstellt.
* *H2*: Long-Wert, der den zweiten Hashwert darstellt.
* *HN*: Long-Wert, der den Nth-Hashwert darstellt.

**Rückgabe**

Der kombinierte Hashwert der angegebenen skalare.

**Beispiele**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|ersten|Halbjahr|erter|
|---|---|---|---|---|
|Hallo|World|753694413698530628|1846988464401551951|-1440138333540407281|
