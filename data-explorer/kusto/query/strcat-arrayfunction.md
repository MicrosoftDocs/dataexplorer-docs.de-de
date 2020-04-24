---
title: strcat_array() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird strcat_array() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d2412762cf68243e3952a8ad12a5b919d947bd3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506952"
---
# <a name="strcat_array"></a>strcat_array()

Erstellt eine verkettete Zeichenfolge von Arraywerten mit einem angegebenen Trennzeichen.
    
**Syntax**

`strcat_array(`*Array*, *Trennzeichen*`)`

**Argumente**

* *array*: `dynamic` Ein Wert, der ein Array von Zuverkettenden Werten darstellt.
* *delimeter*: `string` Ein Wert, der zum Verketten der Werte im *Array* verwendet wird

**Rückgabe**

Arraywerte, verkettet auf eine einzelne Zeichenfolge.

**Beispiele**
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|str|
|---|
|1->2->3|