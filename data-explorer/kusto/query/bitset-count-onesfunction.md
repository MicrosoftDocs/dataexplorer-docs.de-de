---
title: bitset_count_ones() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird bitset_count_ones() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: c23e95ee9f00a0ca173d68d3591ad604b31dfac2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517390"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

Gibt die Anzahl der gesetzten Bits in der binären Darstellung einer Zahl zurück.

```kusto
bitset_count_ones(42)
```

**Syntax**

`bitset_count_ones(`*num1*'')'

**Argumente**

* *num1*: lange oder ganzzahlige Zahl.

**Rückgabe**

Gibt die Anzahl der gesetzten Bits in der binären Darstellung einer Zahl zurück.

**Beispiel**

```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|eins|
|---|
|3|