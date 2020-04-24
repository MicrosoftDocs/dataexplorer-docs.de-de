---
title: tobool() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt tobool() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3e1867c99ae241b3f7e09ab8ee873d5ae5d374e0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506357"
---
# <a name="tobool"></a>tobool()

Konvertiert Die Eingabe in eine boolesche (signierte 8-Bit) Darstellung.

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

**Syntax**

`tobool(`*Expr*`)`
`)` Expr (Alias)*Expr* `toboolean(`

**Argumente**

* *Expr*: Ausdruck, der in boolesche umgewandelt wird. 

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein boolescher Wert.
Wenn die Konvertierung nicht erfolgreich `null`ist, wird das Ergebnis .
 