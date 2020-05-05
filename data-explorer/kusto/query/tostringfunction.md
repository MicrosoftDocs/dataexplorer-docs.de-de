---
title: "\"$ String ()\": Azure-Daten-Explorer"
description: In diesem Artikel wird die Zeichenfolge () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 634f54533e83575139d8399124cc068af56d8574
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741673"
---
# <a name="tostring"></a>tostring()

Konvertiert Eingaben in eine Zeichen folgen Darstellung.

```kusto
tostring(123) == "123"
```

**Syntax**

`tostring(`*`Expr`*`)`

**Argumente**

* *`Expr`*: Ausdruck, der in eine Zeichenfolge konvertiert wird. 

**Rückgabe**

Wenn der *`Expr`* Wert nicht NULL ist, ist das Ergebnis eine Zeichen folgen Darstellung von *`Expr`*.
Wenn der *`Expr`* Wert NULL ist, ist das Ergebnis eine leere Zeichenfolge.
 