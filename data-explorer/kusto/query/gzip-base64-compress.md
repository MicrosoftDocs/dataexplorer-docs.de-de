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
ms.openlocfilehash: 2d5d19a1a0d90a6bcff42247b85d01497afd0fac
ms.sourcegitcommit: 0e2fbc26738371489491a96924f25553a8050d51
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148537"
---
# <a name="gzip_compress_to_base64_string"></a>gzip_compress_to_base64_string ()

Führt die gzip-Komprimierung aus und codiert das Ergebnis in base64.


## <a name="syntax"></a>Syntax

`gzip_compress_to_base64_string("`*Input_string* "`)`

## <a name="arguments"></a>Argumente

*Input_string* : Eingabe `string` , eine zu komprimierende Zeichenfolge und Base64-codiert. Die Funktion akzeptiert ein Zeichen folgen Argument.

## <a name="returns"></a>Rückgabe

* Gibt einen zurück `string` , der eine gzip-komprimierte und eine Base64-codierte Zeichenfolge darstellt. 
* Gibt ein leeres Ergebnis zurück, wenn die Komprimierung oder Codierung fehlgeschlagen ist

## <a name="example"></a>Beispiel
```kusto
print res = gzip_compress_to_base64_string("1234567890qwertyuiop")
```

**Ausgabe:** 
> |" eaebfadr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA = = "|

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie [gzip_decompress_from_base64_string ()](gzip-base64-decompress.md) , um die ursprüngliche unkomprimierte Zeichenfolge abzurufen.
