---
title: Hash ()-Azure Daten-Explorer
description: In diesem Artikel wird Hash () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0a8f1b2a72ceb154b7252b3218718a9c23968cb
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763868"
---
# <a name="hash"></a>hash()

Gibt einen Hashwert für den Eingabe Wert zurück.

**Syntax**

`hash(`*Quelle* [ `,` *mod*]`)`

**Argumente**

* *Quelle*: der Wert, für den der Hashwert verwendet werden soll.
* *mod*: ein optionaler Modulwert, der auf das Hash Ergebnis angewendet werden soll, sodass der Ausgabewert zwischen `0` und *mod* -1 liegt.

**Rückgabe**

Der Hashwert des angegebenen Skalars, Modulo der angegebene mod-Wert (falls angegeben).

> [!WARNING]
> Der Algorithmus, der zum Berechnen des Hashs verwendet wird, ist xxhash.
> Dieser Algorithmus kann sich in Zukunft ändern, und die einzige Garantie besteht darin, dass alle Aufrufe dieser Methode innerhalb einer einzelnen Abfrage denselben Algorithmus verwenden.
> Daher wird empfohlen, die Ergebnisse von nicht `hash()` in einer Tabelle zu speichern. Wenn persistente Hashwerte erforderlich sind, verwenden Sie stattdessen [hash_sha256 ()](./sha256hashfunction.md) oder [hash_md5 ()](./md5hashfunction.md) . Beachten Sie, dass diese Funktionen komplexer zu berechnen sind als `hash()` ).

**Beispiele**

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

Im folgenden Beispiel wird die Hash Funktion verwendet, um eine Abfrage für 10% der Daten auszuführen. es ist hilfreich, die Hash Funktion für die Stichprobenentnahme der Daten zu verwenden, wenn davon ausgegangen wird, dass der Wert gleichmäßig verteilt wird (in diesem Beispiel StartTime-Wert).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
