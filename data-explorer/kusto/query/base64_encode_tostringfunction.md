---
title: base64_encode_tostring ()-Azure Daten-Explorer
description: In diesem Artikel wird base64_encode_tostring () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: c414f1bdb83850bc6ec6065314bc7c8662ab0ed2
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717086"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

Codiert eine Zeichenfolge als base64-Zeichenfolge.

**Syntax**

`base64_encode_tostring(`*Schnür*`)`

**Argumente**

* *String*: als base64-Zeichenfolge zu codierende Eingabe Zeichenfolge.

**Rückgabe**

Gibt die als base64-Zeichenfolge codierte Zeichenfolge zurück

* Informationen zum Decodieren von Base64-Zeichen folgen in UTF-8-Zeichen folgen finden Sie unter [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Informationen zum Decodieren von Base64-Zeichen folgen in ein Array von Long-Werten finden Sie unter [base64_decode_toarray ()](base64_decode_toarrayfunction.md)


**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quin   |
|--------|
|S3VzdG8 =|

