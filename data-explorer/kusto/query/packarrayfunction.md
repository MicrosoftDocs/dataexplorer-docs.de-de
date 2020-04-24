---
title: pack_array() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird pack_array() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ad13efd6b0743a3c092b2859ea032c3731ffdf1b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511865"
---
# <a name="pack_array"></a>pack_array()

Packt alle Eingabewerte in ein dynamisches Array.

**Syntax**

`pack_array(`*Expr1*`[`` *Expr2*]`, )'

**Argumente**

* *Expr1... N*: Eingabeausdrücke, die in ein dynamisches Array gepackt werden sollen.

**Rückgabe**

Dynamisches Array, das die Werte von Expr1, Expr2, ... , ExprN enthält.

**Beispiel**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

|Column1|
|---|
|[1,2,4]|
|[2,4,8]|
|[3,6,12]|

```kusto
range x from 1 to 3 step 1
| extend y = tostring(x * 2)
| extend z = (x * 2) * 1s
| project pack_array(x,y,z)
```

|Column1|
|---|
|[1,"2","00:00:02"]|
|[2,"4","00:00:04"]|
|[3,"6","00:00:06"]|