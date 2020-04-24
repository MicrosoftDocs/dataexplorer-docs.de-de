---
title: make_string() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird make_string() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5af7cab9106088064048c1077ec3f9b1950ec08
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512630"
---
# <a name="make_string"></a>make_string()

Gibt die Zeichenfolge zurück, die von den Unicode-Zeichen generiert wird.
    
**Syntax**

`make_string (`*Arg1* [, *ArgN*]...`)`

**Argumente**

* *Arg1* ... *ArgN* : Ausdrücke, die ganze Zahlen (int oder long) oder ein dynamischer Wert mit einem Array von integralen Zahlen sind.

* Diese Funktion empfängt bis zu 64 Argumente. 

**Rückgabe**

Gibt die Zeichenfolge aus den Unicode-Zeichen zurück, deren Codepointwert von den Argumenten für diese Funktion bereitgestellt wird. Die Eingabe muss aus gültigen Unicode-Codepoints bestehen.
Wenn ein Argument keinem Unicode-Zeichen zugeordnet ist, gibt die Funktion NULL zurück.

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