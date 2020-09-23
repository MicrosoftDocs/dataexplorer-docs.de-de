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
ms.openlocfilehash: b62e5032d6f2ccedc2883b6cbccaf7be69e1cebf
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103505"
---
# <a name="set_has_element"></a>set_has_element()

Bestimmt, ob die angegebene Menge das angegebene Element enthält.

## <a name="syntax"></a>Syntax

`set_has_element(`*Array*,*Wert*`)`

## <a name="arguments"></a>Argumente

* *Array*: zu durchsuchende Eingabe Array.
* *Wert*: Wert, nach dem gesucht werden soll. Der Wert muss vom Typ `long` , `integer` ,, `double` `datetime` , `timespan` , `decimal` , oder sein `string` `guid` .

## <a name="returns"></a>Gibt zurück

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
