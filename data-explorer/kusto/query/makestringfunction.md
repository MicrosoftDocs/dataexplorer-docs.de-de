---
title: make_string ()-Azure Daten-Explorer
description: In diesem Artikel wird make_string () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8f3497e10c15bfd6df0337758d8dc3002419fa1
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252228"
---
# <a name="make_string"></a>make_string()

Gibt die von den Unicode-Zeichen generierte Zeichenfolge zurück.
    
## <a name="syntax"></a>Syntax

`make_string (`*Arg1*[, *argN*]... `)`

## <a name="arguments"></a>Argumente

* *Arg1* ... *ArgN*: Ausdrücke, bei denen es sich um ganze Zahlen (int oder Long) handelt, oder ein dynamischer Wert, der ein Array ganzzahliger Zahlen enthält.

* Diese Funktion empfängt bis zu 64 Argumente.

## <a name="returns"></a>Rückgabe

Gibt die Zeichenfolge zurück, die aus den Unicode-Zeichen besteht, deren codepointwert von den Argumenten für diese Funktion bereitgestellt wird. Die Eingabe muss aus gültigen Unicode-Code Punkten bestehen.
Wenn ein beliebiges Argument keinem Unicode-Zeichen zugeordnet ist, gibt die Funktion zurück `null` .

## <a name="examples"></a>Beispiele

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
