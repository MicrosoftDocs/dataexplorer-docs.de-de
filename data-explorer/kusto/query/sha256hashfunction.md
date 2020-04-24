---
title: hash_sha256() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird hash_sha256() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2147f4e9f2bd3d7df8f75ac704a4e4808e69bb3c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507598"
---
# <a name="hash_sha256"></a>hash_sha256()

Gibt einen sha256-Hashwert für den Eingabewert zurück.

**Syntax**

`hash_sha256(`*Quelle*`)`

**Argumente**

* *quelle*: Der zu hashende Wert.

**Rückgabe**

Der sha256-Hashwert des angegebenen Skalars, kodiert als Hex-Zeichenfolge (eine Zeichenfolge, von denen jeweils zwei eine einzelne Hex-Zahl zwischen 0 und 255 darstellen).

> [!WARNING]
> Der von dieser Funktion verwendete Algorithmus (SHA256) wird garantiert in Zukunft nicht mehr geändert, ist aber sehr komplex zu berechnen. Benutzern, die für die Dauer einer einzelnen Abfrage eine "leichte" Hashfunktion benötigen, wird empfohlen, stattdessen den [Funktionshash()](./hashfunction.md) zu verwenden.

**Beispiele**

```kusto
hash_sha256("World")                   // 78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524
hash_sha256(datetime("2015-01-01"))    // e7ef5635e188f5a36fafd3557d382bbd00f699bd22c671c3dea6d071eb59fbf8
```

Im folgenden Beispiel wird die hash_sha256-Funktion verwendet, um eine Abfrage für die StartTime-Spalte der Daten auszuführen.

```kusto
StormEvents 
| where hash_sha256(StartTime) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```