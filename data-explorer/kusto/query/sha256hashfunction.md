---
title: hash_sha256 ()-Azure Daten-Explorer
description: In diesem Artikel wird hash_sha256 () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 32fa2f3ffefdbf1f14ed87e8e89444de322408c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372314"
---
# <a name="hash_sha256"></a>hash_sha256()

Gibt einen SHA256-Hashwert für den Eingabe Wert zurück.

**Syntax**

`hash_sha256(`*Ausgangs*`)`

**Argumente**

* *Quelle*: der Wert, für den der Hashwert verwendet werden soll.

**Rückgabe**

Der SHA256-Hashwert des angegebenen Skalars, der als hexadezimale Zeichenfolge (eine Zeichenfolge) codiert ist, die jeweils eine einzelne hexadezimal Zahl zwischen 0 und 255 darstellt.

> [!WARNING]
> Der Algorithmus, der von dieser Funktion (SHA256) verwendet wird, wird in Zukunft garantiert nicht geändert, ist jedoch sehr komplex zu berechnen. Für Benutzer, die für die Dauer einer einzelnen Abfrage eine "einfache" Hash Funktion benötigen, wird empfohlen, stattdessen den Funktions [Hash ()](./hashfunction.md) zu verwenden.

**Beispiele**

```kusto
hash_sha256("World")                   // 78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524
hash_sha256(datetime("2015-01-01"))    // e7ef5635e188f5a36fafd3557d382bbd00f699bd22c671c3dea6d071eb59fbf8
```

Im folgenden Beispiel wird die hash_sha256-Funktion verwendet, um eine Abfrage für die StartTime-Spalte der Daten auszuführen.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash_sha256(StartTime) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
