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
ms.openlocfilehash: b8ebef923d1cc67c118317680e1ec414900a469e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348954"
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
