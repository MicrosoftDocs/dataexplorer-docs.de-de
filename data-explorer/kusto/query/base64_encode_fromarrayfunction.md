---
title: base64_encode_fromarray() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird base64_encode_fromarray() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: f601463fd6751be7064892e70e5b2235f96a20ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518070"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

Kodiert eine base64-Zeichenfolge aus einem Bytes-Array.

**Syntax**

`base64_encode_fromarray(`*BytesArray*`)`

**Argumente**

* *BytesArray*: Geben Sie Bytes-Array ein, das in die base64-Zeichenfolge codiert werden soll.

**Rückgabe**

Gibt die base64-Zeichenfolge zurück, die aus dem Bytes-Array codiert ist.

* Informationen zum Decodieren von base64-Zeichenfolgen zu einer UTF-8-Zeichenfolge finden Sie unter [base64_decode_tostring()](base64_decode_tostringfunction.md)
* Informationen zum Codieren von Zeichenfolgen für base64-Zeichenfolgesiehe [base64_encode_tostring()](base64_encode_tostringfunction.md)
* Diese Funktion ist die Umkehrung von [base64_decode_toarray()](base64_decode_toarrayfunction.md)

**Beispiel**

```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8=|


Beim Versuch, eine base64-Zeichenfolge aus einem ungültigen Bytes-Array zu codieren, das von einer ungültigen UTF-8-codierten Zeichenfolge generiert wurde, wird null zurückgegeben:

```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||