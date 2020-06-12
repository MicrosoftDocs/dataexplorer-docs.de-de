---
title: "\"straucat ()\": Azure-Daten-Explorer"
description: In diesem Artikel wird "in Azure Daten-Explorer" beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: af25bb0407c9bc0c004c2f22e326ac034c6682cd
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717103"
---
# <a name="strcat"></a>strcat()

Verkettet zwischen 1 und 64 Argumenten.

* Wenn die Argumente nicht vom Typ "String" sind, werden Sie zwangsweise in eine Zeichenfolge konvertiert.

**Syntax**

`strcat(`*Argument1*, *Argument2*[, *argumentn*]`)`

**Argumente**

* *Argument1* ... *argumentn*: Ausdrücke, die verkettet werden sollen.

**Rückgabe**

Argumente, die zu einer einzelnen Zeichenfolge verkettet werden.

**Beispiele**
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|
