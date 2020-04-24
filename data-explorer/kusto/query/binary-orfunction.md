---
title: binary_or() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird binary_or() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f6d68137ded3b164ca25d82e0c17b6fef6fc1a1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517594"
---
# <a name="binary_or"></a>binary_or()

Gibt ein Ergebnis der `or` bitweisen Operation der beiden Werte zurück. 

```kusto
binary_or(x,y)
```

**Syntax**

`binary_or(`*num1* `,` *num2*`)`

**Argumente**

* *num1*, *num2*: lange Zahlen.

**Rückgabe**

Gibt logische ODER-Operation für ein Zahlenpaar zurück: num1 | num2.