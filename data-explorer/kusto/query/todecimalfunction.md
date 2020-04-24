---
title: todecimal() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt todecimal() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d5ac70dfe71f80c3963292516e1b8516a297875
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506306"
---
# <a name="todecimal"></a>todecimal()

Konvertiert Die Eingabe in die Dezimalzahlendarstellung.

```kusto
todecimal("123.45678") == decimal(123.45678)
```

**Syntax**

`todecimal(`*Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der in Dezimalwert konvertiert wird. 

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis eine Dezimalzahl sein.
Wenn die Konvertierung nicht erfolgreich `null`ist, wird das Ergebnis .
 
*Hinweis*: Bevorzugen Sie die Verwendung von [real()](./scalar-data-types/real.md) wenn möglich.