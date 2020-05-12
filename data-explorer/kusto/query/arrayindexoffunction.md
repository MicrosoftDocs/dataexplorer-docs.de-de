---
title: array_index_of ()-Azure Daten-Explorer
description: In diesem Artikel wird array_index_of () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: 99044d8762a1c7c7e86fb2633a8226ef48d66b55
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225631"
---
# <a name="array_index_of"></a>array_index_of()

Durchsucht das Array nach dem angegebenen Element und gibt seine Position zurück.

**Syntax**

`array_index_of(`*Array*,*Wert*`)`

**Argumente**

* *Array*: zu durchsuchende Eingabe Array.
* *Wert*: Wert, nach dem gesucht werden soll. Der Wert muss vom Typ Long, Integer, Double, DateTime, TimeSpan, Decimal, String oder GUID sein.

**Rückgabe**

Null basierte Indexposition von Lookup.
Gibt-1 zurück, wenn der Wert nicht im Array gefunden wird.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|Ergebnis|
|---|
|3|

**Siehe auch**

Wenn Sie nur überprüfen möchten, ob ein Wert in einem Array vorhanden ist, Sie jedoch nicht an seiner Position interessiert sind, können Sie [set_has_element ( `arr` , `value` )](sethaselementfunction.md)verwenden. Diese Funktion verbessert die Lesbarkeit der Abfrage. Beide Funktionen haben dieselbe Leistung.
