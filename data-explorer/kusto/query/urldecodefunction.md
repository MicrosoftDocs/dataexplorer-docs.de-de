---
title: url_decode() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird url_decode() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3ffa5052a2fc30387be118683ec1df6f34f7346f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505150"
---
# <a name="url_decode"></a>url_decode()

Die Funktion konvertiert codierte URL in eine reguläre URL-Darstellung. 

Ausführliche Informationen zur URL-Dekodierung und -Codierung finden Sie [hier](https://en.wikipedia.org/wiki/Percent-encoding).

**Syntax**

`url_decode(`*codierte URL*`)`

**Argumente**

* *codierte URL*: codierte URL (Zeichenfolge).  

**Rückgabe**

URL (Zeichenfolge) in einer regulären Darstellung.

**Beispiele**

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|original|Decodiert|
|---|---|
|https%3a%2f%2fwww.bing.com%2f|https://www.bing.com/|



 