---
title: make_string ()-Azure Daten-Explorer
description: In diesem Artikel wird make_string () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 035be5910d173c75e585baa0b093dc6276bd4d63
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818584"
---
# <a name="make_string"></a>make_string()

Gibt die von den Unicode-Zeichen generierte Zeichenfolge zurück.
    
**Syntax**

`make_string (`*Arg1*[, *argN*]...`)`

**Argumente**

* *Arg1* ... *ArgN*: Ausdrücke, bei denen es sich um ganze Zahlen (int oder Long) handelt, oder ein dynamischer Wert, der ein Array ganzzahliger Zahlen enthält.

* Diese Funktion empfängt bis zu 64 Argumente.

**Rückgabe**

Gibt die Zeichenfolge zurück, die aus den Unicode-Zeichen besteht, deren codepointwert von den Argumenten für diese Funktion bereitgestellt wird. Die Eingabe muss aus gültigen Unicode-Code Punkten bestehen.
Wenn ein beliebiges Argument keinem Unicode-Zeichen zugeordnet ist, gibt die Funktion zurück `null` .

**Beispiele**

```kusto
print str = make_string(75, 117, 115, 116, 111)
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115, 116, 111]))
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115]), 116, 111)
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(75, 10, 117, 10, 115, 10, 116, 10, 111)
```

|str|
|---|
|K<br>u<br>s<br>t<br>o|
