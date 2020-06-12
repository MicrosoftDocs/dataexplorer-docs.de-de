---
title: base64_decode_toarray ()-Azure Daten-Explorer
description: In diesem Artikel wird base64_decode_toarray () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: df10ea2bbdf4e48d32c087d35eb9361fc4d697b8
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717205"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Decodiert eine base64-Zeichenfolge in ein Array von Long-Werten.

**Syntax**

`base64_decode_toarray(`*Schnür*`)`

**Argumente**

* *String*: Eingabe Zeichenfolge, die von Base64 in UTF8-Zeichenfolge decodiert werden soll.

**Rückgabe**

Gibt ein Array von langen Werten zurück, die aus einer base64-Zeichenfolge decodiert wurden.

* Informationen zum Decodieren von Base64-Zeichen folgen in eine UTF-8-Zeichenfolge finden Sie unter [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Informationen zum Codieren von Zeichen folgen in eine base64-Zeichenfolge finden Sie unter [base64_encode_tostring ()](base64_encode_tostringfunction.md)

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|Quin|
|-----|
|[75.117.115.116.111]|

Wenn Sie versuchen, eine base64-Zeichenfolge zu decodieren, die aus einer ungültigen UTF-8-Codierung generiert wurde, wird "Null" zurückgegeben:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
