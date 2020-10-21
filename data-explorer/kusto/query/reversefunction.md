---
title: Reverse ()-Azure Daten-Explorer
description: Dieser Artikel beschreibt Reverse () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dc348643ff6e098b2291e69d68ca817c1f5af9c2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243001"
---
# <a name="reverse"></a>reverse()

Funktion kehrt die Reihenfolge der Eingabe Zeichenfolge um.
Wenn der Eingabe Wert nicht vom Typ `string` ist, wandelt die Funktion den Wert in den Typ um `string` .

## <a name="syntax"></a>Syntax

`reverse(`*Ausgangs*`)`

## <a name="arguments"></a>Argumente

* *Quelle*: Eingabe Wert.  

## <a name="returns"></a>Rückgabe

Die umgekehrte Reihenfolge eines Zeichen folgen Werts.

## <a name="examples"></a>Beispiele

```kusto
print str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
| extend rstr = reverse(str)
```

|str|RSTR|
|---|---|
|ABCDEFGHIJKLMNOPQRSTUVWXYZ|Zyxwvutsrqponmlkjihgfedcba|


```kusto
print ['int'] = 12345, ['double'] = 123.45, 
['datetime'] = datetime(2017-10-15 12:00), ['timespan'] = 3h
| project rint = reverse(['int']), rdouble = reverse(['double']), 
rdatetime = reverse(['datetime']), rtimespan = reverse(['timespan'])
```

|rint|rdouble|rdatetime|rtimespan|
|---|---|---|---|
|54321|54,321|Z 0000000.00:00:21t51-01-7102|00:00:30|
