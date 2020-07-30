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
ms.openlocfilehash: a366120428d14c713b18aee6652460817c75433a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349566"
---
# <a name="array_index_of"></a>array_index_of()

Durchsucht das Array nach dem angegebenen Element und gibt seine Position zurück.

## <a name="syntax"></a>Syntax

`array_index_of(`*Array*,*Wert*`)`

## <a name="arguments"></a>Argumente

* *Array*: zu durchsuchende Eingabe Array.
* *Wert*: Wert, nach dem gesucht werden soll. Der Wert muss vom Typ Long, Integer, Double, DateTime, TimeSpan, Decimal, String oder GUID sein.

## <a name="returns"></a>Rückgabe

Null basierte Indexposition von Lookup.
Gibt-1 zurück, wenn der Wert nicht im Array gefunden wird.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|Ergebnis|
|---|
|3|

**Weitere Informationen**

Wenn Sie nur überprüfen möchten, ob ein Wert in einem Array vorhanden ist, Sie jedoch nicht an seiner Position interessiert sind, können Sie [set_has_element ( `arr` , `value` )](sethaselementfunction.md)verwenden. Diese Funktion verbessert die Lesbarkeit der Abfrage. Beide Funktionen haben dieselbe Leistung.
