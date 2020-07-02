---
title: hash_md5 ()-Azure Daten-Explorer
description: In diesem Artikel wird hash_md5 () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/29/2020
ms.openlocfilehash: c09f30a4f13f16e15cfcc826f6976f1208fdabf1
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793624"
---
# <a name="hash_md5"></a>hash_md5 ()

Gibt einen MD5-Hashwert für den Eingabe Wert zurück.

**Syntax**

`hash_md5(`*Ausgangs*`)`

**Argumente**

* *Quelle*: der Wert, für den der Hashwert verwendet werden soll.

**Rückgabe**

Der MD5-Hashwert des angegebenen Skalars, der als hexadezimale Zeichenfolge (eine Zeichenfolge) codiert ist, die jeweils eine einzelne hexadezimal Zahl zwischen 0 und 255 darstellt.

> [!WARNING]
> Der Algorithmus, der von dieser Funktion (MD5) verwendet wird, wird in Zukunft garantiert nicht geändert, ist jedoch sehr komplex zu berechnen. Für Benutzer, die für die Dauer einer einzelnen Abfrage eine "einfache" Hash Funktion benötigen, wird empfohlen, stattdessen den Funktions [Hash ()](./hashfunction.md) zu verwenden.

**Beispiele**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
h1=hash_md5("World"),
h2=hash_md5(datetime(2020-01-01))
```

|ersten|Halbjahr|
|---|---|
|f5a7924e621e84c9280a9a27e1bcb7f6|786c530672d1s8db31fee25ea8a9390b|


Im folgenden Beispiel wird die- `hash_md5()` Funktion verwendet, um stormevents basierend auf dem MD5-Hashwert des Zustands zu aggregieren. 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize StormCount = count() by State, StateHash=hash_md5(State)
| top 5 by StormCount
```

|Status|Status|Stormcount|
|---|---|---|
|TEXAS|3b00dbe6e07e7485a1c12d36c8e9910a|4701|
|Kansas|e1338d0ac8be43846cf9ae967bd02e7f|3166|
|IOWA|6d4a7c02942093576149db764d4e2d2|2337|
|Illinois|8c00d9e0b3fcd55aed5657e42cc40cf1|2022|
|Missouri|2d82f 0c963c0763012b2539d469e5008|2016|
