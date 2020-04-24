---
title: hash_combine() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden hash_combine() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: d0c6375436df298a97c03ec2f06f7cd492d59d7f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514262"
---
# <a name="hash_combine"></a>hash_combine()

Kombiniert Hashwerte von zwei oder mehr Hashes.

**Syntax**

`hash_combine(`*h1* `,` *h2* [`,` *h3* ...]`)`

**Argumente**

* *h1*: Long-Wert, der den ersten Hashwert darstellt.
* *h2*: Long-Wert, der den zweiten Hashwert darstellt.
* *hN*: Long-Wert, der den Nth-Hashwert darstellt.

**Rückgabe**

Der kombinierte Hashwert der angegebenen Skalar.

**Beispiele**

```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|h1|h2|Kombiniert|
|---|---|---|---|---|
|Hallo|World|753694413698530628|1846988464401551951|-1440138333540407281|