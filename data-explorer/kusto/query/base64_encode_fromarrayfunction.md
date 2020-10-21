---
title: base64_encode_fromarray ()-Azure Daten-Explorer
description: In diesem Artikel wird base64_encode_fromarray () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 88d9f5b26273fa91ae64ca637b8fe4e2650a8539
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252703"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

Codiert eine base64-Zeichenfolge aus einem Bytearray.

## <a name="syntax"></a>Syntax

`base64_encode_fromarray(`*Bytesarray*`)`

## <a name="arguments"></a>Argumente

* *Bytesarray*: Eingabe Byte Array, das in base64-Zeichenfolge codiert werden soll.

## <a name="returns"></a>Rückgabe

Gibt die base64-Zeichenfolge zurück, die aus dem Byte Array

* Zum Decodieren von Base64-Zeichen folgen in eine UTF-8-Zeichenfolge siehe [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Informationen zum Codieren von Zeichen folgen in Base64-Zeichen folgen [base64_encode_tostring](base64_encode_tostringfunction.md) finden Sie unter
* Diese Funktion ist die Umkehrung von [base64_decode_toarray ()](base64_decode_toarrayfunction.md) .

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8 =|


Wenn Sie versuchen, eine base64-Zeichenfolge aus einem ungültigen Bytearray zu codieren, das aus einer ungültigen UTF-8-codierten Zeichenfolge generiert wurde

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||
