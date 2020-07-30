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
ms.openlocfilehash: f71a0d0cdfa4fe0ca8cdb84e65a271ee42bc7dc7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347611"
---
# <a name="hash_combine"></a>hash_combine()

Kombiniert Hashwerte von zwei oder mehr Hashes.

## <a name="syntax"></a>Syntax

`hash_combine(`*H1* `,` *H2* [ `,` *H3* ...]`)`

## <a name="arguments"></a>Argumente

* *H1*: Long-Wert, der den ersten Hashwert darstellt.
* *H2*: Long-Wert, der den zweiten Hashwert darstellt.
* *HN*: Long-Wert, der den Nth-Hashwert darstellt.

## <a name="returns"></a>Gibt zurück

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
