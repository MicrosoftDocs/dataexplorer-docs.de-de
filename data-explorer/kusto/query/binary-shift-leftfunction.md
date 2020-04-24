---
title: binary_shift_left() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird binary_shift_left() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 15e2bee789e627709ccfedde8eccead7f2578b51
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517560"
---
# <a name="binary_shift_left"></a>binary_shift_left()

Gibt den binären Shift-Links-Vorgang für ein Zahlenpaar zurück.

```kusto
binary_shift_left(x,y)  
```

**Syntax**

`binary_shift_left(`*num1* `,` *num2*`)`

**Argumente**

* *num1*, *num2*: int zahlen.

**Rückgabe**

Gibt binäre Verschiebung links Operation auf einem Paar von Zahlen: num1 << (num2%64).
Wenn n negativ ist, wird ein NULL-Wert zurückgegeben.