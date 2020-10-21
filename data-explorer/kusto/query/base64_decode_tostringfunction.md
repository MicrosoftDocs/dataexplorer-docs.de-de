---
title: base64_decode_tostring ()-Azure Daten-Explorer
description: In diesem Artikel wird base64_decode_tostring () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: eab1065713bcbc73913a2ab17a99894d9af9f8a4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252717"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

Decodiert eine base64-Zeichenfolge in eine UTF-8-Zeichenfolge.

## <a name="syntax"></a>Syntax

`base64_decode_tostring(`*Schnür*`)`

## <a name="arguments"></a>Argumente

* *String*: die Eingabe Zeichenfolge, die von Base64 in UTF8-8-Zeichenfolge decodiert werden soll.

## <a name="returns"></a>Rückgabe

Gibt die aus base64-Zeichenfolge decodierte UTF-8-Zeichenfolge zurück

* Informationen zum Decodieren von Base64-Zeichen folgen in ein Array von Long-Werten finden Sie unter [base64_decode_toarray ()](base64_decode_toarrayfunction.md)
* Informationen zum Decodieren von Zeichen folgen in Base64-Zeichen folgen finden Sie unter [base64_encode_tostring ()](base64_encode_tostringfunction.md)

## <a name="example"></a>Beispiel

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

|Leer|
|-----|
||
