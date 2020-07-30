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
ms.openlocfilehash: 2093ff1117cf7744af550cf93c3fe630fa40a6e6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340173"
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
 