---
title: base64_decode_toarray ()-Azure Daten-Explorer
description: In diesem Artikel wird base64_decode_toarray () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 34a4601c35acb9c95e09e49d201b2e1cddaace8c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252734"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Decodiert eine base64-Zeichenfolge in ein Array von Long-Werten.

## <a name="syntax"></a>Syntax

`base64_decode_toarray(`*Schnür*`)`

## <a name="arguments"></a>Argumente

* *String*: Eingabe Zeichenfolge, die von Base64 in UTF8-Zeichenfolge decodiert werden soll.

## <a name="returns"></a>Rückgabe

Gibt ein Array von langen Werten zurück, die aus einer base64-Zeichenfolge decodiert wurden.

* Informationen zum Decodieren von Base64-Zeichen folgen in eine UTF-8-Zeichenfolge finden Sie unter [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Informationen zum Codieren von Zeichen folgen in eine base64-Zeichenfolge finden Sie unter [base64_encode_tostring ()](base64_encode_tostringfunction.md)

## <a name="example"></a>Beispiel

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

|Leer|
|-----|
||
