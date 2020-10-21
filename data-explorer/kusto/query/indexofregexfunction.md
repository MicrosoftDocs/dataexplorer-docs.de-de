---
title: indexof_regex ()-Azure Daten-Explorer
description: In diesem Artikel wird indexof_regex () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 472fdea209cc416893043f15b3796862ef91fa82
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243269"
---
# <a name="indexof_regex"></a>indexof_regex()

Die Funktion meldet den NULL basierten Index des ersten Vorkommens einer angegebenen Zeichenfolge in der Eingabe Zeichenfolge. Einfache Zeichen folgen Übereinstimmungen überlappen nicht.

Siehe [`indexof()`](indexoffunction.md).

## <a name="syntax"></a>Syntax

`indexof_regex(`*Quelle* `,` *Suche* `[,` *start_index* `[,` *Länge* `[,` *vorkommen*`]]])`

## <a name="arguments"></a>Argumente

|Argumente     | BESCHREIBUNG                                     |Erforderlich oder optional|
|--------------|-------------------------------------------------|--------------------|
|source        | Eingabezeichenfolge                                    |Erforderlich            |
|lookup        | Zu suchender Zeichenfolge                                  |Erforderlich            |
|start_index   | Startposition der Suche                           |Optional            |
|length        | Anzahl der zu überprüfenden Zeichen Positionen. -1 definiert eine unbegrenzte Länge. |Optional            |
|occurrence    | Suchen Sie nach dem Index der N-ten Darstellung des Musters. 
                 Der Standardwert ist 1, der Index des ersten Vorkommens. |Optional            |

## <a name="returns"></a>Rückgabe

Null basierte Indexposition von *Suche*.

* Gibt-1 zurück, wenn die Zeichenfolge nicht in der Eingabe gefunden wurde.
* Gibt *null* zurück, wenn:
     * start_index ist kleiner als 0 (null).
     * Das Vorkommen ist kleiner als 0 (null).
     * der length-Parameter ist kleiner als-1.


## <a name="examples"></a>Beispiele

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
