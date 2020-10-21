---
title: base64_encode_tostring ()-Azure Daten-Explorer
description: In diesem Artikel wird base64_encode_tostring () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 6fca71cbc7cf33f49a9f4698553391a84ddbb728
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252662"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

Codiert eine Zeichenfolge als base64-Zeichenfolge.

## <a name="syntax"></a>Syntax

`base64_encode_tostring(`*Schnür*`)`

## <a name="arguments"></a>Argumente

* *String*: als base64-Zeichenfolge zu codierende Eingabe Zeichenfolge.

## <a name="returns"></a>Rückgabe

Gibt die als base64-Zeichenfolge codierte Zeichenfolge zurück

* Informationen zum Decodieren von Base64-Zeichen folgen in UTF-8-Zeichen folgen finden Sie unter [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Informationen zum Decodieren von Base64-Zeichen folgen in ein Array von Long-Werten finden Sie unter [base64_decode_toarray ()](base64_decode_toarrayfunction.md)


## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quin   |
|--------|
|S3VzdG8 =|

