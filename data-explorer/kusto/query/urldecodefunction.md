---
title: url_decode ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird url_decode () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0019e318b90f9626d9e55a593f19526cdc7cc9c7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350586"
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



 