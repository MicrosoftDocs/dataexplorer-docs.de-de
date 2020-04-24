---
title: url_encode() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird url_encode() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 913be2d20af413f8ba89192f4db57e60fc6d7b27
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505048"
---
# <a name="url_encode"></a>url_encode()

Die Funktion konvertiert Zeichen der Eingabe-URL in ein Format, das über das Internet übertragen werden kann. 

Ausführliche Informationen zur URL-Codierung und -Dekodierung finden Sie [hier](https://en.wikipedia.org/wiki/Percent-encoding).
Unterscheidet sich von [url_encode_component](./urlencodecomponentfunction.md) durch Codierung von Leerzeichen als '+' und nicht als '20%' (siehe anwendung/x-www-form-urlencoded [hier](https://en.wikipedia.org/wiki/Percent-encoding)).

**Syntax**

`url_encode(`*Url*`)`

**Argumente**

* *url*: Eingabe-URL (Zeichenfolge).  

**Rückgabe**

URL (Zeichenfolge) in ein Format konvertiert, das über das Internet übertragen werden kann.

**Beispiele**

```kusto
let url = @'https://www.bing.com/hello word';
print original = url, encoded = url_encode(url)
```

|original|Codiert|
|---|---|
|https://www.bing.com/hellowort/|https%3a%2f%2fwww.bing.com%2fhello+word|


 