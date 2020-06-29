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
ms.openlocfilehash: b7a94d8c4e4f22deed6cd9368cdd52dd15e86dd7
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2020
ms.locfileid: "85517782"
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

<!-- csl: https://kuskusdfv3.kusto.windows.net/Kuskus -->
```kusto
print 
h1=hash_sha256("World"),
h2=hash_sha256(datetime(2020-01-01))
```

|ersten|Halbjahr|
|---|---|
|78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524|ba666752dc1a20eb750b0eb64e780cc4c968bc9fb8813461c1d7e750f302d71d|

Im folgenden Beispiel wird die- `hash_sha256()` Funktion verwendet, um stormevents basierend auf dem SHA256-Hashwert des Zustands zu aggregieren. 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count() by State, StateHash=hash_sha256(State)
| top 5 by StormCount desc
```

|Status|Status|Stormcount|
|---|---|---|
|TEXAS|9087f20f23f91b5a77e8406846117049029e6798ebbd0d38aea68da73a00ca37|4701|
|Kansas|c80e328393541a3181b258cdb4da4d00587c5045e8cf3bb6c8fdb7016b69cc2e|3166|
|IOWA|f85893dca466f779410f65cd904fdc4622de49e119ad4e7c7e4a291ceed1820b|2337|
|Illinois|ae3eeabfd7eba3d9a4ccbfed6a9b8cff269dc43255906476282e0184cf81b7fd|2022|
|Missouri|d15dfc28abc3ee73b7d1f664a35980167ca96f6f90e034db2a6525c0b8ba61b1|2016|
