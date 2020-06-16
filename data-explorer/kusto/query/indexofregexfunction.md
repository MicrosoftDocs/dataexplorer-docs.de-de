---
title: indexof_regex ()-Azure Daten-Explorer
description: In diesem Artikel wird indexof_regex () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 72797b54c3ba431b4a846f9e9661e9693359cceb
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780455"
---
# <a name="indexof_regex"></a>indexof_regex()

Die Funktion meldet den NULL basierten Index des ersten Vorkommens einer angegebenen Zeichenfolge in der Eingabe Zeichenfolge. Einfache Zeichen folgen Übereinstimmungen überlappen nicht.

Siehe [`indexof()`](indexoffunction.md).

**Syntax**

`indexof_regex(`*Quelle* `,` *Suche* `[,` *start_index* `[,` *Länge* `[,` *vorkommen*`]]])`

**Argumente**

|Argumente     | BESCHREIBUNG                                     |Erforderlich oder optional|
|--------------|-------------------------------------------------|--------------------|
|source        | Eingabezeichenfolge                                    |Erforderlich            |
|lookup        | Zu suchender Zeichenfolge                                  |Erforderlich            |
|start_index   | Startposition der Suche                           |Optional            |
|length        | Anzahl der zu überprüfenden Zeichen Positionen. -1 definiert eine unbegrenzte Länge. |Optional            |
|occurrence    | Suchen Sie nach dem Index der N-ten Darstellung des Musters. 
                 Der Standardwert ist 1, der Index des ersten Vorkommens. |Optional            |

**Rückgabe**

Null basierte Indexposition von *Suche*.

* Gibt-1 zurück, wenn die Zeichenfolge nicht in der Eingabe gefunden wurde.
* Gibt *null* zurück, wenn:
     * start_index ist kleiner als 0 (null).
     * Das Vorkommen ist kleiner als 0 (null).
     * der length-Parameter ist kleiner als-1.


**Beispiele**

```kusto
print
 idx1 = indexof_regex("abcabc", "a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", "a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", "a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", "a.a", 0, -1, 2)  // Plain string matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", "a|ab", -1)  // invalid input
```

|idx1|idx2|idx3|idx4|idx5|
|----|----|----|----|----|
|0   |3   |-1  |-1  |    |
