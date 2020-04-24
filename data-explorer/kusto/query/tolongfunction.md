---
title: tolong() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt tolong() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd354a39c048631ab98390c74cb7cd78ee5376b2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506085"
---
# <a name="tolong"></a>tolong()

Konvertiert Die Eingabe in eine lange (signierte 64-Bit) Zahlendarstellung.

```kusto
tolong("123") == 123
```

**Syntax**

`tolong(`*Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der in long konvertiert wird. 

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis eine lange Zahl.
Wenn die Konvertierung nicht erfolgreich `null`ist, wird das Ergebnis .
 
*Hinweis*: Bevorzugen Sie die Verwendung von [long()](./scalar-data-types/long.md) wenn möglich.