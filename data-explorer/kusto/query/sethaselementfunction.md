---
title: set_has_element ()-Azure Daten-Explorer
description: In diesem Artikel wird set_has_element () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 9cf2ec4371f4aeef8a68cb65fb2b946b9c393054
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372378"
---
# <a name="set_has_element"></a>set_has_element()

Bestimmt, ob die angegebene Menge das angegebene Element enthält.

**Syntax**

`set_has_element(`*Array*,*Wert*`)`

**Argumente**

* *Array*: zu durchsuchende Eingabe Array.
* *Wert*: Wert, nach dem gesucht werden soll. Der Wert muss vom Typ `long` , `integer` ,, `double` `datetime` , `timespan` , `decimal` , oder sein `string` `guid` .

**Rückgabe**

True oder false, abhängig davon, ob der Wert im Array vorhanden ist.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|Ergebnis|
|---|
|1|

**Siehe auch**

Wenn Sie auch an der Position interessiert sind, an der der Wert im Array vorhanden ist, können Sie [array_index_of (arr, Wert)](arrayindexoffunction.md)verwenden. Beide Funktionen sind identisch, leistungsorientiert.
