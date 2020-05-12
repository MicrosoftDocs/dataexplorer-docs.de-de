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
ms.openlocfilehash: eda367dfeaab15dc5249fd860596964c597a1bcd
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225410"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Decodiert eine base64-Zeichenfolge in ein Array von Long-Werten.

**Syntax**

`base64_decode_toarray(`*Schnür*`)`

**Argumente**

* *String*: die Eingabe Zeichenfolge, die von Base64 in UTF8-8-Zeichenfolge decodiert werden soll.

**Rückgabe**

Gibt ein Array von Long-Werten aus der base64-Zeichenfolge zurück

* Zum Decodieren von Base64-Zeichen folgen in eine UTF-8-Zeichenfolge siehe [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Informationen zum Codieren von Zeichen folgen in Base64-Zeichen folgen [base64_encode_tostring](base64_encode_tostringfunction.md) finden Sie unter

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|Quin|
|-----|
|[75.117.115.116.111]|

Beim Decodieren einer base64-Zeichenfolge, die aus einer ungültigen UTF-8-Codierung generiert wurde, wird NULL zurückgegeben:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
