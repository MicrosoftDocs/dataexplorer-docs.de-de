---
title: "\"-Daten-Explorer\" (): Azure-"
description: In diesem Artikel wird "in Azure Daten-Explorer" beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7fa3d12b1dfc1fcbede2f5f47b3841102b72e5a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250594"
---
# <a name="tobool"></a>tobool()

Konvertiert Eingaben in eine boolesche Darstellung (8-Bit-Darstellung mit Vorzeichen).

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

## <a name="syntax"></a>Syntax

`tobool(`*Expr* `)` 
 Expr `toboolean(` *Expr* `)` Alias

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der in einen booleschen Wert konvertiert wird. 

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein boolescher Wert.
Wenn die Konvertierung nicht erfolgreich ist, wird das Ergebnis zurückgeben `null` .
