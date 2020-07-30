---
title: url_encode_component ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird url_encode_component () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 44e77e2a0e34b66506852bd82d65e4a108b520fd
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338455"
---
# <a name="url_encode_component"></a>url_encode_component()

Die-Funktion konvertiert Zeichen der Eingabe-URL in ein Format, das über das Internet übermittelt werden kann. 

Ausführliche Informationen zur URL-Codierung und-Decodierung finden Sie [hier](https://en.wikipedia.org/wiki/Percent-encoding).
Unterscheidet sich von [url_encode](./urlencodefunction.md) , indem Leerzeichen als "20%" und nicht als "+" codiert werden.

## <a name="syntax"></a>Syntax

`url_encode_component(`*Urne*`)`

## <a name="arguments"></a>Argumente

* *URL*: Eingabe-URL (Zeichenfolge).  

## <a name="returns"></a>Gibt zurück

Die URL (Zeichenfolge) wurde in ein Format konvertiert, das über das Internet übermittelt werden kann.

## <a name="examples"></a>Beispiele

```kusto
let url = @'https://www.bing.com/hello word/';
print original = url, encoded = url_encode_component(url)
```

|original|codierte|
|---|---|
|https://www.bing.com/helloWort|HTTPS %3 a %2 f %2 f www. b. com% 2F %20 Word|


 