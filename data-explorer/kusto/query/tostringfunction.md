---
title: tostring() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt tostring() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 51aaf90b60653a648457dc00200168aec7fbefd9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505881"
---
# <a name="tostring"></a>tostring()

Konvertiert Die Eingabe in eine Zeichenfolgendarstellung.

```kusto
tostring(123) == "123"
```

**Syntax**

`tostring(`*Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der in Zeichenfolge konvertiert wird. 

**Rückgabe**

Wenn *Expr-Wert* ist un-NULL-Ergebnis ist, ist eine Zeichenfolgendarstellung von *Expr*.
Wenn *Expr-Wert* null ist, ist das Ergebnis leere Zeichenfolge.
 