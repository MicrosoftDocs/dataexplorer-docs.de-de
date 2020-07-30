---
title: count ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird "count ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d34b0611db134a6fc99daa49d04bfc19575a1c1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348750"
---
# <a name="countof"></a>countof()

Zählt die Vorkommnisse einer Teilzeichenfolge in einer Zeichenfolge. Einfache Zeichenfolgenübereinstimmungen überlappen sich möglicherweise; bei regex-Übereinstimmungen ist dies nicht Fall.

```kusto
countof("The cat sat on the mat", "at") == 3
countof("The cat sat on the mat", @"\b.at\b", "regex") == 3
```

## <a name="syntax"></a>Syntax

`countof(`*Text* `,` *Suche* [ `,` *Kind*]`)`

## <a name="arguments"></a>Argumente

* *Text*: eine Zeichenfolge.
* *Search*: die einfache Zeichenfolge oder der [reguläre Ausdruck](./re2.md) , der in *Text*abgeglichen werden soll.
* *Art*: `"normal"|"regex"` Standard `normal` . 

## <a name="returns"></a>Rückgabe

Angabe, wie oft die Suchzeichenfolge im Container abgeglichen werden kann. Einfache Zeichenfolgenübereinstimmungen überlappen sich möglicherweise; bei regex-Übereinstimmungen ist dies nicht Fall.

## <a name="examples"></a>Beispiele

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (nicht 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    