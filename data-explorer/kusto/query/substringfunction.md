---
title: 'substring(): Azure Data Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt substring() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7b2f2dc18fe12f4bd07b638b6c3ca32d95a10618
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512042"
---
# <a name="substring"></a>substring()

Extrahiert eine Teilzeichenfolge aus einer Quellzeichenfolge beginnend bei einem Index bis zum Ende der Zeichenfolge.

Optional kann die Länge der angeforderten Teilzeichenfolge angegeben werden.

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>Syntax

`substring(`*source*`,` *startingIndex* [`,` *length*]`)`

## <a name="arguments"></a>Argumente

* *source:* Die Quellzeichenfolge, aus der die Teilzeichenfolge entnommen wird.
* *startingIndex*: Die nullbasierte Position des Anfangszeichens der angeforderten Teilzeichenfolge.
* *length*: Ein optionaler Parameter, der zur Angabe der angeforderten Anzahl von Zeichen in der Teilzeichenfolge verwendet werden kann. 

**Hinweise**

*startingIndex* kann eine negative Zahl sein. In diesem Fall wird die Teilzeichenfolge vom Ende der Quellzeichenfolge abgerufen.

## <a name="returns"></a>Gibt zurück

Eine Teilzeichenfolge aus der angegebenen Zeichenfolge. Die Teilzeichenfolge beginnt bei der startingIndex (nullbasierten) Zeichenposition und wird bis zum Ende der Zeichenfolge oder der Längenzeichen fortgesetzt, sofern angegeben.

## <a name="examples"></a>Beispiele

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```