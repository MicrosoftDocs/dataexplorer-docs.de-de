---
title: binary_and() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird binary_and() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c5501218c1ddb69a73f685fda78f3b3482afb5c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517679"
---
# <a name="binary_and"></a>binary_and()

Gibt ein Ergebnis der `and` bitweisen Operation zwischen zwei Werten zurück.

```kusto
binary_and(x,y) 
```

**Syntax**

`binary_and(`*num1* `,` *num2*`)`

**Argumente**

* *num1*, *num2*: lange Zahlen.

**Rückgabe**

Gibt den logischen UND-Vorgang für ein Zahlenpaar zurück: num1 & num2.