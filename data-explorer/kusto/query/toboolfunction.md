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
ms.openlocfilehash: e0343ae5cb98e1cb3114e24c963fe2981be82c5b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350790"
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
