---
title: bitset_count_ones ()-Azure Daten-Explorer
description: In diesem Artikel wird bitset_count_ones () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: f8abb1683a2f15f012e9a9271681688c19901af0
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227593"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

Gibt die Anzahl der Satz Bits in der binären Darstellung einer Zahl zurück.

```kusto
bitset_count_ones(42)
```

**Syntax**

`bitset_count_ones(`*num1*' ') '

**Argumente**

* *num1*: Long-oder Integer-Zahl.

**Rückgabe**

Gibt die Anzahl der Satz Bits in der binären Darstellung einer Zahl zurück.

**Beispiel**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|Stürme|
|---|
|3|
