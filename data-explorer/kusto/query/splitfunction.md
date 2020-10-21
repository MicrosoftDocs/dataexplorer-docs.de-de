---
title: Split ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Split () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8ce8af3f9d56e4b5c3d388010b2760906a8e3dc4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242431"
---
# <a name="split"></a>split()

Teilt eine angegebene Zeichenfolge gemäß einem angegebenen Trennzeichen und gibt ein Zeichen folgen Array mit den enthaltenen Teil Zeichenfolgen zurück.

Optional kann eine bestimmte Teilzeichenfolge zurückgegeben werden, sofern vorhanden.

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>Syntax

`split(`*Quelle* `,` *Trenn* Zeichen [ `,` *requestedindex*]`)`

## <a name="arguments"></a>Argumente

* *Source*: die Quell Zeichenfolge, die entsprechend dem angegebenen Trennzeichen aufgeteilt wird.
* *delimiter:* Das Trennzeichen, das zum Teilen der Quellzeichenfolge verwendet wird.
* *requestedIndex*: Ein optionaler nullbasierter Index (`int`). Sofern angegeben, enthält das zurückgegebene Zeichenfolgenarray die angeforderte Teilzeichenfolge, sofern vorhanden. 

## <a name="returns"></a>Rückgabe

Ein Zeichenfolgenarray, das die Teilzeichenfolgen der angegebenen Quellzeichenfolge enthält, die durch das angegebene Trennzeichen getrennt sind.

## <a name="examples"></a>Beispiele

```kusto
print
    split("aa_bb", "_"),           // ["aa","bb"]
    split("aaa_bbb_ccc", "_", 1),  // ["bbb"]
    split("", "_"),                // [""]
    split("a__b", "_"),            // ["a","","b"]
    split("aabbcc", "bb")          // ["aa","cc"]
```