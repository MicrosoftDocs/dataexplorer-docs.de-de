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
ms.openlocfilehash: 332ff6bedd268dd79be020ff1dc4d0591ed486f7
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225308"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

Codiert eine Zeichenfolge als base64-Zeichenfolge.

**Syntax**

`base64_encode_tostring(`*Schnür*`)`

**Argumente**

* *String*: als base64-Zeichenfolge zu codierende Eingabe Zeichenfolge.

**Rückgabe**

Gibt die als base64-Zeichenfolge codierte Zeichenfolge zurück

* Zum Decodieren von Base64-Zeichen folgen in eine UTF-8-Zeichenfolge siehe [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Informationen zum Decodieren von Base64-Zeichen folgen in ein Array mit langen Werten finden Sie [base64_decode_toarray ()](base64_decode_toarrayfunction.md)


**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quin   |
|--------|
|S3VzdG8 =|
