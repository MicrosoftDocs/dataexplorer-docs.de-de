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
ms.openlocfilehash: 218f87a3c11695c4aa8135f98b0c445580de9ad0
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349260"
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

