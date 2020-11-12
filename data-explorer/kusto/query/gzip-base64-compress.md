---
title: 'gzip_compress_to_base64_string: Azure Daten-Explorer'
description: In diesem Artikel wird der gzip_compress_to_base64_string ()-Befehl in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 11/01/2020
ms.openlocfilehash: fffa39ca5fa41c065971b4feebfe60752b84ed59
ms.sourcegitcommit: 3eabd78305d32cd9b8a6bd1d76877ddc19d8ac63
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548868"
---
# <a name="gzip_compress_to_base64_string"></a>gzip_compress_to_base64_string()

Führt die gzip-Komprimierung aus und codiert das Ergebnis in base64.


## <a name="syntax"></a>Syntax

`gzip_compress_to_base64_string("`*Input_string* "`)`

## <a name="arguments"></a>Argumente

*Input_string* : Eingabe `string` , eine zu komprimierende Zeichenfolge und Base64-codiert. Die Funktion akzeptiert ein Zeichen folgen Argument.

## <a name="returns"></a>Gibt zurück

* Gibt einen zurück `string` , der eine gzip-komprimierte und eine Base64-codierte Zeichenfolge darstellt. 
* Gibt ein leeres Ergebnis zurück, wenn die Komprimierung oder Codierung fehlgeschlagen ist

## <a name="example"></a>Beispiel
```kusto
print res = gzip_compress_to_base64_string("1234567890qwertyuiop")
```

**Ausgabe:** 

| H4sIAAAAAAAA/weuaov/MTIzNDU2Nzg5MHF3ZXJ0eXVpb3A6m7f2FAAAAA = = |

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie [gzip_decompress_from_base64_string ()](gzip-base64-decompress.md) , um die ursprüngliche unkomprimierte Zeichenfolge abzurufen.
* Siehe auch [zlib_compress_to_base64_string ()](zlib-base64-compress.md)
