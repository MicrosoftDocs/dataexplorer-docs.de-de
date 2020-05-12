---
title: hash_many ()-Azure Daten-Explorer
description: In diesem Artikel wird hash_many () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: 66ca1e5ff330a4b39ab769b0e3e8d6359eed9c00
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226668"
---
# <a name="hash_many"></a>hash_many()

Gibt einen kombinierten Hashwert mit mehreren Werten zurück.

**Syntax**

`hash_many(`*S1* `,` *S2* [ `,` *S3* ...]`)`

**Argumente**

* *S1*, *S2*,..., *SN*: Eingabewerte, für die ein Hashwert erstellt wird.

**Rückgabe**

Der kombinierte Hashwert der angegebenen skalare.

**Beispiele**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|erter|
|---|---|---|
|Hallo|World|-1440138333540407281|
