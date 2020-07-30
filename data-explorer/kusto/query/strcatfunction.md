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
ms.openlocfilehash: 1f26b4bf267a4387748fe4c4c26636579607de51
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350994"
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
