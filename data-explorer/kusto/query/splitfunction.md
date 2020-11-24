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
ms.localizationpriority: high
ms.openlocfilehash: 4baae5bee8dd1e85a304be7fb4eae988acc404d8
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512943"
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

## <a name="returns"></a>Gibt zurück

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