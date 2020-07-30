---
title: Reverse ()-Azure Daten-Explorer
description: Dieser Artikel beschreibt Reverse () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fa87dae85b158e162cea90c276057abb210ea23b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345775"
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
