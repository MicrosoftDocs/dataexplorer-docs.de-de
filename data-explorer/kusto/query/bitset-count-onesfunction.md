---
title: bitset_count_ones ()-Azure Daten-Explorer
description: In diesem Artikel wird bitset_count_ones () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: fb14ffa5807a61ade913631c7d6da6b23edc26d9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245326"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

Gibt die Anzahl der Satz Bits in der binären Darstellung einer Zahl zurück.

```kusto
bitset_count_ones(42)
```

## <a name="syntax"></a>Syntax

`bitset_count_ones(`*num1*' ') '

## <a name="arguments"></a>Argumente

* *num1*: Long-oder Integer-Zahl.

## <a name="returns"></a>Rückgabe

Gibt die Anzahl der Satz Bits in der binären Darstellung einer Zahl zurück.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|Stürme|
|---|
|3|
