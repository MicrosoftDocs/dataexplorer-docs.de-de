---
title: "\"-Daten-Explorer\" (): Azure-"
description: In diesem Artikel wird "in Azure Daten-Explorer" beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f99406d94e1cd64da8605e5000aa99136c2b119a
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763761"
---
# <a name="tobool"></a>tobool()

Konvertiert Eingaben in eine boolesche Darstellung (8-Bit-Darstellung mit Vorzeichen).

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

**Syntax**

`tobool(`*Expr* `)` 
 Expr `toboolean(` *Expr* `)` Alias

**Argumente**

* *Expr*: Ausdruck, der in einen booleschen Wert konvertiert wird. 

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein boolescher Wert.
Wenn die Konvertierung nicht erfolgreich ist, wird das Ergebnis zurückgeben `null` .
