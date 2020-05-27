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
ms.openlocfilehash: 8e96b97754600d308526a4cb059907521fda0521
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83862927"
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

Verwenden [`array_index_of(arr, value)`](arrayindexoffunction.md) Sie, um die Position zu ermitteln, an der der Wert im Array vorhanden ist. Beide Funktionen sind gleichwertig.
