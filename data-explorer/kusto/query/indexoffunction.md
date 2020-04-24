---
title: indexof() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt indexof() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 698cc7c13c3d665f9f5cfe25a31269dc763c51fb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513939"
---
# <a name="indexof"></a>indexof()

Function meldet den nullbasierten Index des ersten Vorkommens einer angegebenen Zeichenfolge innerhalb der Eingabezeichenfolge.

Wenn Die Such- oder Eingabezeichenfolge nicht vom Zeichenfolgentyp ist, wird der Wert zwangsweise in string umgeworfen.

Siehe [`indexof_regex()`](indexofregexfunction.md).

**Syntax**

`indexof(`*Quellsuche*`,``[,`*lookup*`[,`*start_index-Längen-Vorkommen*`[,`*occurrence* *start_index*`]]])`

**Argumente**

* *Quelle*: Eingabezeichenfolge.  
* *Lookup*: Zeichenfolge zu suchen.
* *start_index*: Startposition suchen (optional).
* *Länge*: Anzahl der zu untersuchenden Zeichenpositionen, -1 Definition unbegrenzter Länge (optional).
* *Vorkommen*: ist das Vorkommen Standard 1 (optional).

**Rückgabe**

Nullbasierte Indexposition der *Suche*.

Gibt -1 zurück, wenn die Zeichenfolge in der Eingabe nicht gefunden wird.

Bei irrelevantem (weniger als 0) *start_index*gibt *der Vorkommen* oder (weniger als -1) *Längenparameter* *null*zurück.

**Beispiele**
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
