---
title: array_index_of() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden array_index_of() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: f68c9385c55cedb4491033d137af087dafd26aa0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518614"
---
# <a name="array_index_of"></a>array_index_of()

Durchsucht das Array nach dem angegebenen Element und gibt dessen Position zurück.

**Syntax**

`array_index_of(`*Array*,*Wert*`)`

**Argumente**

* *Array*: Eingabe-Array zu suchen.
* *Wert*: Wert, nach dem gesucht werden soll. Der Wert sollte vom Typ long, integer, double, datetime, timespan, decimal, string oder guid sein.

**Rückgabe**

Nullbasierte Indexposition der Suche.
Gibt -1 zurück, wenn der Wert nicht im Array gefunden wird.

**Beispiel**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|Ergebnis|
|---|
|3|

**Siehe auch**

Wenn Sie nur überprüfen möchten, ob ein Wert in einem Array vorhanden ist, Aber Sie sich nicht für seine Position interessieren, können Sie [set_has_element(arr, value)](sethaselementfunction.md) verwenden - dies verbessert die Lesbarkeit Ihrer Abfrage, aber leistungsmäßig sind beide Funktionen identisch.