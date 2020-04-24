---
title: substring() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Substring() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0273b3e93c8778af9c380f164faec74349aa8cd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506697"
---
# <a name="substring"></a>substring()

Extrahiert eine Teilzeichenfolge aus einer Quellzeichenfolge, beginnend mit einem Index bis zum Ende der Zeichenfolge.

Optional kann die Länge der angeforderten Teilzeichenfolge angegeben werden.

```kusto
substring("abcdefg", 1, 2) == "bc"
```

**Syntax**

`substring(`*Quelle* `,` *startingIndex* [`,` *Länge*]`)`

**Argumente**

* *source*: Die Quellzeichenfolge, aus der die Teilzeichenfolge entnommen wird.
* *startingIndex*: Die nullbasierte Startzeichenposition der angeforderten Teilzeichenfolge.
* *length*: Ein optionaler Parameter, der verwendet werden kann, um die angeforderte Anzahl von Zeichen in der Teilzeichenfolge anzugeben. 

**Hinweise**

*startingIndex* kann eine negative Zahl sein, in diesem Fall wird die Teilzeichenfolge vom Ende der Quellzeichenfolge abgerufen.

**Rückgabe**

Eine Teilzeichenfolge aus der angegebenen Zeichenfolge. Die Teilzeichenfolge beginnt bei der startingIndex (nullbasierten) Zeichenposition und wird bis zum Ende der Zeichenfolge oder der Längenzeichen fortgesetzt, sofern angegeben.

**Beispiele**

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```