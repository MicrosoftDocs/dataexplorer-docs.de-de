---
title: IndexOf ()-Azure Daten-Explorer
description: In diesem Artikel wird IndexOf () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1558e2463c2958965fcb501aff99c7ec14fe8688
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252953"
---
# <a name="indexof"></a>indexof()

Meldet den NULL basierten Index des ersten Vorkommens einer angegebenen Zeichenfolge in der Eingabe Zeichenfolge.

Wenn die Suche oder die Eingabe Zeichenfolge nicht vom Typ " *String* " ist, wandelt die Funktion den Wert in eine *Zeichenfolge*um.

Weitere Informationen finden Sie unter [`indexof_regex()`](indexofregexfunction.md).

## <a name="syntax"></a>Syntax

`indexof(`*Quelle* `,` *Suche* `[,` *start_index* `[,` *Länge* `[,` *vorkommen*`]]])`

## <a name="arguments"></a>Argumente

* *Quelle*: Eingabe Zeichenfolge.  
* *Suche*: Zeichenfolge, die gesucht werden soll.
* *start_index*: Startposition der Suche. Dies ist optional.
* *length*: Anzahl der zu überprüfenden Zeichen Positionen. Der Wert-1 bedeutet eine unbegrenzte Länge. Dies ist optional.
* *vorkommen*: die Anzahl der vorkommen. Standardwert: 1. Optional.

## <a name="returns"></a>Rückgabe

Null basierte Indexposition von *Suche*.

Gibt-1 zurück, wenn die Zeichenfolge nicht in der Eingabe gefunden wurde.

Wenn irrelevant (kleiner als 0) *start_index*, *vorkommen*oder (kleiner als-1) *length* -Parameter, wird *null*zurückgegeben.

## <a name="examples"></a>Beispiele
```kusto
print
 idx1 = indexof("abcdefg","cde")    // lookup found in input string
 , idx2 = indexof("abcdefg","cde",1,4) // lookup found in researched range 
 , idx3 = indexof("abcdefg","cde",1,2) // search starts from index 1, but stops after 2 chars, so full lookup can't be found
 , idx4 = indexof("abcdefg","cde",3,4) // search starts after occurrence of lookup
 , idx5 = indexof("abcdefg","cde",-1)  // invalid input
 , idx6 = indexof(1234567,5,1,4)       // two first parameters were forcibly casted to strings "12345" and "5"
 , idx7 = indexof("abcdefg","cde",2,-1)  // lookup found in input string
 , idx8 = indexof("abcdefgabcdefg", "cde", 1, 10, 2)   // lookup found in input range
 , idx9 = indexof("abcdefgabcdefg", "cde", 1, -1, 3)   // the third occurrence of lookup is not in researched range
```

|idx1|idx2|idx3|idx4|idx5|idx6|idx7|idx8|idx9|
|----|----|----|----|----|----|----|----|----|
|2   |2   |-1  |-1  |    |4   |2   |9   |-1  |
