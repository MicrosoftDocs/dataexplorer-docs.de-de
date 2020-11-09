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
ms.openlocfilehash: b2bed8fab33de57da6286a7ea49b9378669a9d5a
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373849"
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

| eaebfadr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA = = |

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie [gzip_decompress_from_base64_string ()](gzip-base64-decompress.md) , um die ursprüngliche unkomprimierte Zeichenfolge abzurufen.
