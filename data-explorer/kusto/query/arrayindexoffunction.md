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
ms.openlocfilehash: 27b956ee54ef22f55b3a0ceae97fceb41aadf5c3
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737350"
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

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|Ergebnis|
|---|
|3|

**Siehe auch**

Wenn Sie nur überprüfen möchten, ob ein Wert in einem Array vorhanden ist, Sie jedoch nicht an seiner Position interessiert sind, können Sie [set_has_element`arr`( `value`,)](sethaselementfunction.md)verwenden. Diese Funktion verbessert die Lesbarkeit der Abfrage. Beide Funktionen haben dieselbe Leistung.
