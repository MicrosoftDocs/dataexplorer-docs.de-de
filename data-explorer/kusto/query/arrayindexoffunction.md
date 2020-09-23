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
ms.openlocfilehash: 4b3cd0996b4c60362c7377c06621b140c29203a9
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103040"
---
# <a name="array_index_of"></a>array_index_of()

Durchsucht das Array nach dem angegebenen Element und gibt seine Position zurück.

## <a name="syntax"></a>Syntax

`array_index_of(`*Array*,*Wert*`)`

## <a name="arguments"></a>Argumente

* *Array*: zu durchsuchende Eingabe Array.
* *Wert*: Wert, nach dem gesucht werden soll. Der Wert muss vom Typ Long, Integer, Double, DateTime, TimeSpan, Decimal, String oder GUID sein.

## <a name="returns"></a>Gibt zurück

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

## <a name="see-also"></a>Weitere Informationen

Wenn Sie nur überprüfen möchten, ob ein Wert in einem Array vorhanden ist, Sie jedoch nicht an seiner Position interessiert sind, können Sie [set_has_element ( `arr` , `value` )](sethaselementfunction.md)verwenden. Diese Funktion verbessert die Lesbarkeit der Abfrage. Beide Funktionen haben dieselbe Leistung.
