---
title: countof() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt countof() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d932fbcea9b38849e7d7de09230c9a5aa9fa8e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516897"
---
# <a name="countof"></a>countof()

Zählt die Vorkommnisse einer Teilzeichenfolge in einer Zeichenfolge. Einfache Zeichenfolgenübereinstimmungen überlappen sich möglicherweise; bei regex-Übereinstimmungen ist dies nicht Fall.

```kusto
countof("The cat sat on the mat", "at") == 3
countof("The cat sat on the mat", @"\b.at\b", "regex") == 3
```

**Syntax**

`countof(`*Textsuche* `,` *search* `,` [ *Art*]`)`

**Argumente**

* *text*: Eine Zeichenfolge.
* *suche*: Die einfache Zeichenfolge oder [der reguläre Ausdruck,](./re2.md) die im *Text*übereinstimmen soll.
* *Art* `"normal"|"regex"` : `normal`Standard . 

**Rückgabe**

Angabe, wie oft die Suchzeichenfolge im Container abgeglichen werden kann. Einfache Zeichenfolgenübereinstimmungen überlappen sich möglicherweise; bei regex-Übereinstimmungen ist dies nicht Fall.

**Beispiele**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (nicht 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    