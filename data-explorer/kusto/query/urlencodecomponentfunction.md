---
title: url_encode_component ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird url_encode_component () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 6740effd6a6117a2e63b5d03f09b38a723055f30
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241133"
---
# <a name="url_encode_component"></a>url_encode_component()

Die-Funktion konvertiert Zeichen der Eingabe-URL in ein Format, das über das Internet übermittelt werden kann. 

Ausführliche Informationen zur URL-Codierung und-Decodierung finden Sie [hier](https://en.wikipedia.org/wiki/Percent-encoding).
Unterscheidet sich von [url_encode](./urlencodefunction.md) , indem Leerzeichen als "20%" und nicht als "+" codiert werden.

## <a name="syntax"></a>Syntax

`url_encode_component(`*Urne*`)`

## <a name="arguments"></a>Argumente

* *URL*: Eingabe-URL (Zeichenfolge).  

## <a name="returns"></a>Rückgabe

Die URL (Zeichenfolge) wurde in ein Format konvertiert, das über das Internet übermittelt werden kann.

## <a name="examples"></a>Beispiele

```kusto
let url = @'https://www.bing.com/hello word/';
print original = url, encoded = url_encode_component(url)
```

|original|codierte|
|---|---|
|https://www.bing.com/hello Wort|HTTPS %3 a %2 f %2 f www. b. com% 2F %20 Word|


 