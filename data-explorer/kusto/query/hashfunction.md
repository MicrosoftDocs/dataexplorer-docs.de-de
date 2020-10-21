---
title: Hash ()-Azure Daten-Explorer
description: In diesem Artikel wird Hash () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d840ba106079a85435ee88f8b73cfc6daa7e4c5b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252319"
---
# <a name="hash"></a>hash()

Gibt einen Hashwert für den Eingabe Wert zurück.

## <a name="syntax"></a>Syntax

`hash(`*Quelle* [ `,` *mod*]`)`

## <a name="arguments"></a>Argumente

* *Quelle*: der Wert, für den der Hashwert verwendet werden soll.
* *mod*: ein optionaler Modulwert, der auf das Hash Ergebnis angewendet werden soll, sodass der Ausgabewert zwischen `0` und *mod* -1 liegt.

## <a name="returns"></a>Rückgabe

Der Hashwert des angegebenen Skalars, Modulo der angegebene mod-Wert (falls angegeben).

> [!WARNING]
> Der Algorithmus, der zum Berechnen des Hashs verwendet wird, ist xxhash.
> Dieser Algorithmus kann sich in Zukunft ändern, und die einzige Garantie besteht darin, dass alle Aufrufe dieser Methode innerhalb einer einzelnen Abfrage denselben Algorithmus verwenden.
> Daher wird empfohlen, die Ergebnisse von nicht `hash()` in einer Tabelle zu speichern. Wenn persistente Hashwerte erforderlich sind, verwenden Sie stattdessen [hash_sha256 ()](./sha256hashfunction.md) oder [hash_md5 ()](./md5hashfunction.md) . Beachten Sie, dass diese Funktionen komplexer zu berechnen sind als `hash()` ).

## <a name="examples"></a>Beispiele

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
