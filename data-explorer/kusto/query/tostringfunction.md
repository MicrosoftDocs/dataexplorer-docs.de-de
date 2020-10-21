---
title: "\"$ String ()\": Azure-Daten-Explorer"
description: In diesem Artikel wird die Zeichenfolge () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 43797dcaa5e1a18b6a84f15fc0af89d88d814ff6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243804"
---
# <a name="tostring"></a>tostring()

Konvertiert Eingaben in eine Zeichen folgen Darstellung.

```kusto
tostring(123) == "123"
```

## <a name="syntax"></a>Syntax

`tostring(`*`Expr`*`)`

## <a name="arguments"></a>Argumente

* *`Expr`*: Ausdruck, der in eine Zeichenfolge konvertiert wird. 

## <a name="returns"></a>Rückgabe

Wenn der *`Expr`* Wert nicht NULL ist, ist das Ergebnis eine Zeichen folgen Darstellung von *`Expr`* .
Wenn der *`Expr`* Wert NULL ist, ist das Ergebnis eine leere Zeichenfolge.
 