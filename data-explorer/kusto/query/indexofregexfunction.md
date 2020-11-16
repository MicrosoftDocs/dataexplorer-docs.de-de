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
ms.openlocfilehash: 5826920a411235166002b6833ed88869fb85f211
ms.sourcegitcommit: 88f8ad67711a4f614d65d745af699d013d01af32
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638970"
---
# <a name="indexof_regex"></a>indexof_regex()

Gibt den NULL basierten Index des ersten Vorkommens eines angegebenen regulären Ausdrucks in der Eingabe Zeichenfolge zurück.

Siehe [`indexof()`](indexoffunction.md).

## <a name="syntax"></a>Syntax

`indexof_regex(`*Quelle* `,` *Suche* `[,` *start_index* `[,` *Länge* `[,` *vorkommen*`]]])`

## <a name="arguments"></a>Argumente

|Argumente     | BESCHREIBUNG                                     |Erforderlich oder optional|
|--------------|-------------------------------------------------|--------------------|
|source        | Eingabezeichenfolge                                    |Erforderlich            |
|lookup        | Such Zeichenfolge für reguläre Ausdrücke.               |Erforderlich            |
|start_index   | Startposition der Suche                           |Optional            |
|length        | Anzahl der zu überprüfenden Zeichen Positionen. -1 definiert eine unbegrenzte Länge. |Optional            |
|occurrence    | Suchen Sie nach dem Index der N-ten Darstellung des Musters. 
                 Der Standardwert ist 1, der Index des ersten Vorkommens. |Optional            |

## <a name="returns"></a>Gibt zurück

Null basierte Indexposition von *Suche*.

* Gibt-1 zurück, wenn die Zeichenfolge nicht in der Eingabe gefunden wurde.
* Gibt *null* zurück, wenn:
     * start_index ist kleiner als 0 (null).
     * Das Vorkommen ist kleiner als 0 (null).
     * der length-Parameter ist kleiner als-1.

> [!NOTE]
- Die Suche überlappende Übereinstimmungen wird nicht unterstützt.
- Zeichen folgen für reguläre Ausdrücke können Zeichen enthalten, die entweder Escapezeichen oder die Verwendung von @ ' ' String-Literals erfordern.

## <a name="examples"></a>Beispiele

```kusto
print
 idx1 = indexof_regex("abcabc", @"a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", @"a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", @"a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", @"a.a", 0, -1, 2)  // Matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", @"a|ab", -1)  // invalid start_index argument
```

|idx1|idx2|idx3|idx4|idx5|
|----|----|----|----|----|
|0   |3   |-1  |-1  |    |
