---
title: gzip_decompress_from_base64_string ()-Azure Daten-Explorer
description: In diesem Artikel wird der gzip_decompress_from_base64_string ()-Befehl in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 11/01/2020
ms.openlocfilehash: 19fc8c7ce74cfe632034722fda2b23c5105d013a
ms.sourcegitcommit: 0e2fbc26738371489491a96924f25553a8050d51
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148534"
---
# <a name="gzip_decompress_from_base64_string"></a>gzip_decompress_from_base64_string ()

Decodiert die Eingabe Zeichenfolge aus Base64 und führt gzip-decodeinkomprimierung durch.

## <a name="syntax"></a>Syntax

`gzip_decompress_from_base64_string("`*input_string*`")`

## <a name="arguments"></a>Argumente

*Input_string* : Eingabe `string` , die mit gzip komprimiert und dann Base64-codiert wurde. Die Funktion akzeptiert ein Zeichen folgen Argument.

## <a name="returns"></a>Rückgabe

* Gibt eine zurück `string` , die die ursprüngliche Zeichenfolge darstellt. 
* Gibt ein leeres Ergebnis zurück, wenn Dekomprimierung oder Decodierung fehlgeschlagen ist 
    * Beispielsweise wird bei ungültigen gzip-komprimierten und Base 64-codierten Zeichen folgen eine leere Ausgabe zurückgegeben.

## <a name="examples"></a>Beispiele

```kusto
print res=gzip_decompress_from_base64_string("eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA==")
```

**Ausgabe:**

|" 1234567890qwertyuiop "|

Beispiel für ungültige Eingabe:

```kusto
print res=gzip_decompress_from_base64_string("x0x0x0")
```

**Ausgabe:**
>||

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine komprimierte Eingabe Zeichenfolge mit [gzip_compress_to_base64_string ()](gzip-base64-compress.md).
