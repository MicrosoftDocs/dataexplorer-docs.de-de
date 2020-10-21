---
title: set_has_element ()-Azure Daten-Explorer
description: In diesem Artikel wird set_has_element () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: b32fd7361d98c47c8e93814db6b794a762fad1cf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247212"
---
# <a name="set_has_element"></a>set_has_element()

Bestimmt, ob die angegebene Menge das angegebene Element enthält.

## <a name="syntax"></a>Syntax

`set_has_element(`*Array*,*Wert*`)`

## <a name="arguments"></a>Argumente

* *Array*: zu durchsuchende Eingabe Array.
* *Wert*: Wert, nach dem gesucht werden soll. Der Wert muss vom Typ `long` , `integer` ,, `double` `datetime` , `timespan` , `decimal` , oder sein `string` `guid` .

## <a name="returns"></a>Rückgabe

True oder false, abhängig davon, ob der Wert im Array vorhanden ist.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|Ergebnis|
|---|
|1|

## <a name="see-also"></a>Weitere Informationen

Verwenden [`array_index_of(arr, value)`](arrayindexoffunction.md) Sie, um die Position zu ermitteln, an der der Wert im Array vorhanden ist. Beide Funktionen sind gleichwertig.
