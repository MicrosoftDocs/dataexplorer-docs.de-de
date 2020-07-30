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
ms.openlocfilehash: 314244c58eca6082b9042b263e6b3e6faeb69840
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351198"
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

**Weitere Informationen**

Verwenden [`array_index_of(arr, value)`](arrayindexoffunction.md) Sie, um die Position zu ermitteln, an der der Wert im Array vorhanden ist. Beide Funktionen sind gleichwertig.
