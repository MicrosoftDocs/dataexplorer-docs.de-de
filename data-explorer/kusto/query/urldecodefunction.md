---
title: url_decode ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird url_decode () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b58fea5d367cf31b495b23a09bc0a0dcb6bb95c6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251862"
---
# <a name="url_decode"></a>url_decode()

Die-Funktion konvertiert die codierte URL in eine reguläre URL-Darstellung. 

Ausführliche Informationen zur URL-Decodierung und-Codierung finden Sie [hier](https://en.wikipedia.org/wiki/Percent-encoding).

## <a name="syntax"></a>Syntax

`url_decode(`*codierte URL*`)`

## <a name="arguments"></a>Argumente

* *codierte URL*: codierte URL (Zeichenfolge).  

## <a name="returns"></a>Rückgabe

URL (Zeichenfolge) in regulärer Darstellung.

## <a name="examples"></a>Beispiele

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|original|decodierte|
|---|---|
|HTTPS %3 a %2 f %2 f www.... com% 2F|https://www.bing.com/|



 