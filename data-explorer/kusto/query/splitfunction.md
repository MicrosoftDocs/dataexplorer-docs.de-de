---
title: Split ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Split () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: efd3812086631451b77ca1edd846ec9bd75990fe
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351011"
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