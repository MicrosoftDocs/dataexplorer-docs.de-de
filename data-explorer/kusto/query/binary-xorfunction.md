---
title: binary_xor() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden binary_xor() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2f487aa44f8885cbb443c31b8bb3a503e1a76fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517526"
---
# <a name="binary_xor"></a>binary_xor()

Gibt ein Ergebnis der `xor` bitweisen Operation der beiden Werte zurück.

```kusto
binary_xor(x,y)
```

**Syntax**

`binary_xor(`*num1* `,` *num2*`)`

**Argumente**

* *num1*, *num2*: lange Zahlen.

**Rückgabe**

Gibt den logischen XOR-Vorgang für ein Zahlenpaar zurück: num1 num2.