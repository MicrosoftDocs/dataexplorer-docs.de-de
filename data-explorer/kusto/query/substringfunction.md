---
title: Teil Zeichenfolge ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Teil Zeichenfolge () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7b2f2dc18fe12f4bd07b638b6c3ca32d95a10618
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512042"
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

## <a name="returns"></a>Gibt zurück

Eine Teilzeichenfolge aus der angegebenen Zeichenfolge. Die Teilzeichenfolge beginnt bei der startingIndex (nullbasierten) Zeichenposition und wird bis zum Ende der Zeichenfolge oder der Längenzeichen fortgesetzt, sofern angegeben.

## <a name="examples"></a>Beispiele

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```