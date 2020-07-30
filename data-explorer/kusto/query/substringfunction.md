---
title: Teil Zeichenfolge ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Teil Zeichenfolge () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0e83e8d0baf33e5c11cb8b7ecafa607a08fe32b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350858"
---
# <a name="substring"></a>substring()

Extrahiert eine Teil Zeichenfolge aus einer Quell Zeichenfolge beginnend mit einem Index bis zum Ende der Zeichenfolge.

Optional kann die Länge der angeforderten Teilzeichenfolge angegeben werden.

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>Syntax

`substring(`*Quelle* `,` *startingIndex* [ `,` *Länge*]`)`

## <a name="arguments"></a>Argumente

* *Source*: die Quell Zeichenfolge, aus der die Teil Zeichenfolge entnommen wird.
* *startingIndex*: die null basierte anfangs Zeichenposition der angeforderten Teil Zeichenfolge.
* *length*: ein optionaler Parameter, der verwendet werden kann, um die angeforderte Anzahl von Zeichen in der Teil Zeichenfolge anzugeben. 

**Hinweise**

*startingIndex* kann eine negative Zahl sein. in diesem Fall wird die Teil Zeichenfolge vom Ende der Quell Zeichenfolge abgerufen.

## <a name="returns"></a>Rückgabe

Eine Teilzeichenfolge aus der angegebenen Zeichenfolge. Die Teilzeichenfolge beginnt bei der startingIndex (nullbasierten) Zeichenposition und wird bis zum Ende der Zeichenfolge oder der Längenzeichen fortgesetzt, sofern angegeben.

## <a name="examples"></a>Beispiele

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```