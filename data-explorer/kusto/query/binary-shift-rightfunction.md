---
title: binary_shift_right() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird binary_shift_right() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a94c8c695f1c5d16ee0a7e3a92882486b8a8ef5d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517543"
---
# <a name="binary_shift_right"></a>binary_shift_right()

Gibt binäre Verschiebung rechts Operation auf einem Paar von Zahlen.

```kusto
binary_shift_right(x,y) 
```

**Syntax**

`binary_shift_right(`*num1* `,` *num2*`)`

**Argumente**

* *num1*, *num2*: lange Zahlen.

**Rückgabe**

Gibt binäre Verschiebung rechts Operation auf einem Paar von Zahlen: num1 >> (num2%64).
Wenn n negativ ist, wird ein NULL-Wert zurückgegeben.