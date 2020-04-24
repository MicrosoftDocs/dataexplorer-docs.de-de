---
title: hash_many() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird hash_many() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: e98f9d1d956d15cd7a61e7873f9b1dd34c6ae288
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514177"
---
# <a name="hash_many"></a>hash_many()

Gibt einen kombinierten Hashwert mehrerer Werte zurück.

**Syntax**

`hash_many(`*s1* `,` *s2* [`,` *s3* ...]`)`

**Argumente**

* *s1*, *s2*, ..., *sN*: Eingabewerte, die zusammen gehasht werden.

**Rückgabe**

Der kombinierte Hashwert der angegebenen Skalar.

**Beispiele**

```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|Kombiniert|
|---|---|---|
|Hallo|World|-1440138333540407281|