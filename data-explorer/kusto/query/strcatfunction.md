---
title: strcat() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt strcat() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dd01f875b45be038371cc184987aa2a8f8b8d5eb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506918"
---
# <a name="strcat"></a>strcat()

Verkettet zwischen 1 und 64 Argumenten.

* Wenn Argumente nicht vom Zeichenfolgentyp sind, werden sie zwangsweise in Zeichenfolgen konvertiert.

**Syntax**

`strcat(`*Argument1*,*argument2* [, *argumentN*]`)`

**Argumente**

* *Argument1* ... *argumentN* : Ausdrücke, die verkettet werden sollen.

**Rückgabe**

Argumente, die auf eine einzelne Zeichenfolge verkettet sind.

**Beispiele**
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|