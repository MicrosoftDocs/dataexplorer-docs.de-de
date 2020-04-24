---
title: set_has_element() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird set_has_element() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 256e01646c6ecd39a8a589299acd6620008ece28
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507768"
---
# <a name="set_has_element"></a>set_has_element()

Bestimmt, ob der angegebene Satz das angegebene Element enthält.

**Syntax**

`set_has_element(`*Array*,*Wert*`)`

**Argumente**

* *Array*: Eingabe-Array zu suchen.
* *Wert*: Wert, nach dem gesucht werden soll. Der Wert sollte `long`vom `integer` `double`Typ `datetime` `timespan`, `decimal` `string`, `guid`, , , , , oder .

**Rückgabe**

True oder false, je nachdem, ob der Wert im Array vorhanden ist.

**Beispiel**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|Ergebnis|
|---|
|1|

**Siehe auch**

Wenn Sie auch an der Position interessiert sind, an der der Wert im Array vorhanden ist, können Sie [array_index_of(arr, value)](arrayindexoffunction.md)verwenden. Beide Funktionen sind leistungsmäßig gleich.