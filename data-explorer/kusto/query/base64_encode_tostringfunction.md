---
title: base64_encode_tostring() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird base64_encode_tostring() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: a80b0aa0e3f7e5f330da87f93bbad44e587bcdaf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518053"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

Kodiert eine Zeichenfolge als base64-Zeichenfolge

**Syntax**

`base64_encode_tostring(`*Schnur*`)`

**Argumente**

* *String*: Eingabezeichenfolge, die als base64-Zeichenfolge codiert werden soll.

**Rückgabe**

Gibt die Zeichenfolge zurück, die als base64-Zeichenfolge codiert ist.

* Informationen zum Decodieren von base64-Zeichenfolgen zu einer UTF-8-Zeichenfolge finden Sie unter [base64_decode_tostring()](base64_decode_tostringfunction.md)
* Informationen zum Decodieren von base64-Zeichenfolgen zu einem Array von Long-Werten finden Sie unter [base64_decode_toarray()](base64_decode_toarrayfunction.md)


**Beispiel**

```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quine   |
|--------|
|S3VzdG8=|
