---
title: base64_decode_tostring() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden base64_decode_tostring() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: a821fb07d62d5bca3982cc4c48b8e0457641f3f2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518172"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

Decodes einer base64-Zeichenfolge in eine UTF-8-Zeichenfolge

**Syntax**

`base64_decode_tostring(`*Schnur*`)`

**Argumente**

* *String*: Eingabezeichenfolge, die von base64 in UTF8-8-Zeichenfolge decodiert werden soll.

**Rückgabe**

Gibt UTF-8-Zeichenfolge aus base64-Zeichenfolge dekodiert zurück.

* Informationen zum Decodieren von base64-Zeichenfolgen zu einem Array von Long-Werten finden Sie unter [base64_decode_toarray()](base64_decode_toarrayfunction.md)
* Informationen zum Codieren von Zeichenfolgen für base64-Zeichenfolgesiehe [base64_encode_tostring()](base64_encode_tostringfunction.md)

**Beispiel**

```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|Quine|
|-----|
|Kusto|

Beim Versuch, eine base64-Zeichenfolge zu dekodieren, die von der ungültigen UTF-8-Codierung generiert wurde, wird null zurückgegeben:

```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Leer|
|-----|
||