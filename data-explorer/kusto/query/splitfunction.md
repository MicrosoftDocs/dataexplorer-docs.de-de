---
title: 'split(): Azure Data Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt split() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 4baae5bee8dd1e85a304be7fb4eae988acc404d8
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512943"
---
# <a name="split"></a>split()

Teilt eine angegebene Zeichenfolge gemäß einem angegebenen Trennzeichen und gibt ein Zeichenfolgenarray mit den enthaltenen Teilzeichenfolgen zurück.

Optional kann eine bestimmte Teilzeichenfolge zurückgegeben werden, sofern vorhanden.

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>Syntax

`split(`*source*`,` *delimiter* [`,` *requestedIndex*]`)`

## <a name="arguments"></a>Argumente

* *source:* Die Quellzeichenfolge, die dem angegebenen Trennzeichen entsprechend geteilt wird.
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