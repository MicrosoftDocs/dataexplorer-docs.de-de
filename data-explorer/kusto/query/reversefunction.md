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
ms.openlocfilehash: 22fe505eb8fd391e7a61120dbf42c214cb61c120
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264826"
---
# <a name="reverse"></a>reverse()

Funktion kehrt die Reihenfolge der Eingabe Zeichenfolge um.
Wenn der Eingabe Wert nicht vom Typ `string` ist, wandelt die Funktion den Wert in den Typ um `string` .

**Syntax**

`reverse(`*Ausgangs*`)`

**Argumente**

* *Quelle*: Eingabe Wert.  

**Rückgabe**

Die umgekehrte Reihenfolge eines Zeichen folgen Werts.

**Beispiele**

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
