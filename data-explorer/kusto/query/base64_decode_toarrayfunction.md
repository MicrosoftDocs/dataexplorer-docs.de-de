---
title: base64_decode_toarray() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird base64_decode_toarray() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 80a702f112fd4d7b88b4011b3f615cf34acff62c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518189"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Decodiert eine base64-Zeichenfolge in ein Array langer Werte.

**Syntax**

`base64_decode_toarray(`*Schnur*`)`

**Argumente**

* *String*: Eingabezeichenfolge, die von base64 in UTF8-8-Zeichenfolge decodiert werden soll.

**Rückgabe**

Gibt array von long-Werten ecoded aus base64 string zurück.

* Informationen zum Decodieren von base64-Zeichenfolgen zu einer UTF-8-Zeichenfolge finden Sie unter [base64_decode_tostring()](base64_decode_tostringfunction.md)
* Informationen zum Codieren von Zeichenfolgen für base64-Zeichenfolgesiehe [base64_encode_tostring()](base64_encode_tostringfunction.md)

**Beispiel**

```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|Quine|
|-----|
|[75,117,115,116,111]|

Beim Versuch, eine base64-Zeichenfolge zu dekodieren, die von der ungültigen UTF-8-Codierung generiert wurde, wird null zurückgegeben:

```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Leer|
|-----|
||