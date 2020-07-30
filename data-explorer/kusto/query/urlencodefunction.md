---
title: url_encode ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird url_encode () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 8ccc93286073003bdaf8324611888d32f60910fb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350569"
---
# <a name="url_encode"></a>url_encode()

Die-Funktion konvertiert Zeichen der Eingabe-URL in ein Format, das über das Internet übermittelt werden kann. 

Ausführliche Informationen zur URL-Codierung und-Decodierung finden Sie [hier](https://en.wikipedia.org/wiki/Percent-encoding).
Unterscheidet sich von [url_encode_component](./urlencodecomponentfunction.md) , indem Leerzeichen als "+" und nicht als "20%" codiert werden (siehe "application/x-www-form-urlencoded" [hier](https://en.wikipedia.org/wiki/Percent-encoding)).

## <a name="syntax"></a>Syntax

`url_encode(`*Urne*`)`

## <a name="arguments"></a>Argumente

* *URL*: Eingabe-URL (Zeichenfolge).  

## <a name="returns"></a>Rückgabe

Die URL (Zeichenfolge) wurde in ein Format konvertiert, das über das Internet übermittelt werden kann.

## <a name="examples"></a>Beispiele

```kusto
let url = @'https://www.bing.com/hello word';
print original = url, encoded = url_encode(url)
```

|original|codierte|
|---|---|
|https://www.bing.com/helloWort|HTTPS %3 a %2 f %2 f www.... com% 2F + Word|


 