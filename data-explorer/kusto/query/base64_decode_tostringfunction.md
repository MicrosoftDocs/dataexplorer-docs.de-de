---
title: base64_decode_tostring ()-Azure Daten-Explorer
description: In diesem Artikel wird base64_decode_tostring () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: d2e1c5dbb845e67a5306ccb16c234de383d3ca69
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225342"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

Decodiert eine base64-Zeichenfolge in eine UTF-8-Zeichenfolge.

**Syntax**

`base64_decode_tostring(`*Schnür*`)`

**Argumente**

* *String*: die Eingabe Zeichenfolge, die von Base64 in UTF8-8-Zeichenfolge decodiert werden soll.

**Rückgabe**

Gibt die aus base64-Zeichenfolge decodierte UTF-8-Zeichenfolge zurück

* Informationen zum Decodieren von Base64-Zeichen folgen in ein Array mit langen Werten finden Sie [base64_decode_toarray ()](base64_decode_toarrayfunction.md)
* Informationen zum Codieren von Zeichen folgen in Base64-Zeichen folgen [base64_encode_tostring](base64_encode_tostringfunction.md) finden Sie unter

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|Quin|
|-----|
|Kusto|

Beim Decodieren einer base64-Zeichenfolge, die aus einer ungültigen UTF-8-Codierung generiert wurde, wird NULL zurückgegeben:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
