---
title: "\"straucat ()\": Azure-Daten-Explorer"
description: In diesem Artikel wird "in Azure Daten-Explorer" beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 55a52ff4aece3fa1a3197db0fb47984217292136
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251528"
---
# <a name="strcat"></a>strcat()

Verkettet zwischen 1 und 64 Argumenten.

* Wenn die Argumente nicht vom Typ "String" sind, werden Sie zwangsweise in eine Zeichenfolge konvertiert.

## <a name="syntax"></a>Syntax

`strcat(`*Argument1*, *Argument2*[, *argumentn*]`)`

## <a name="arguments"></a>Argumente

* *Argument1* ... *argumentn*: Ausdrücke, die verkettet werden sollen.

## <a name="returns"></a>Rückgabe

Argumente, die zu einer einzelnen Zeichenfolge verkettet werden.

## <a name="examples"></a>Beispiele
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|
