---
title: indexof_regex() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird indexof_regex() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6da0523e85bab4883c50708ffe3f7d087fdd8c8f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513888"
---
# <a name="indexof_regex"></a>indexof_regex()

Function meldet den nullbasierten Index des ersten Vorkommens einer angegebenen Zeichenfolge innerhalb der Eingabezeichenfolge. Einfache Zeichenfolgenübereinstimmungen überlappen sich nicht. 

Siehe [`indexof()`](indexoffunction.md).

**Syntax**

`indexof_regex(`*Quellsuche*`,``[,`*lookup*`[,`*start_index-Längen-Vorkommen*`[,`*occurrence* *start_index*`]]])`

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
 idx1 = indexof_regex("abcabc", "a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", "a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", "a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", "a.a", 0, -1, 2)  // Plain string matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", "a|ab", -1)  // invalid input
```

|idx1|idx2|idx3|idx4|idx5
|----|----|----|----|----
|0   |3   |-1  |-1  |    