---
title: hash() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt hash() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f8142c42dcb0874dfbd84515e56dc8765bcba3d7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514143"
---
# <a name="hash"></a>hash()

Gibt einen Hashwert für den Eingabewert zurück.

**Syntax**

`hash(`*Quelle* `,` [ *mod*]`)`

**Argumente**

* *quelle*: Der zu hashende Wert.
* *mod*: Ein optionaler Modulwert, der auf das Hashergebnis `0` angewendet werden soll, so dass der Ausgabewert zwischen und *mod* - 1 liegt

**Rückgabe**

Der Hash-Wert des angegebenen Skalars, modulo den angegebenen Mod-Wert (falls angegeben).

> [!WARNING]
> Der Algorithmus, der zum Berechnen des Hashs verwendet wird, ist xxhash.
> Dieser Algorithmus könnte sich in zukunftig ändern, und die einzige Garantie ist, dass innerhalb einer einzigen Abfrage alle Aufrufe dieser Methode den gleichen Algorithmus verwenden.
> Daher wird Benutzern empfohlen, die `hash()` Ergebnisse einer Tabelle nicht zu speichern. Wenn weiterhin Hashwerte erforderlich sind, sollten Sie stattdessen [hash_sha256()](./sha256hashfunction.md) verwenden (beachten `hash()`Sie jedoch, dass die Berechnung wesentlich komplexer ist als ).

**Beispiele**

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

Im folgenden Beispiel verwendet die Hash-Funktion, um eine Abfrage für 10 % der Daten auszuführen, Es ist hilfreich, die Hash-Funktion zum Sampling der Daten zu verwenden, wenn angenommen wird, dass der Wert gleichmäßig verteilt ist (in diesem Beispiel StartTime-Wert)

```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```