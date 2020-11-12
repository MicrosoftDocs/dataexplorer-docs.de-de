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
ms.openlocfilehash: a35fd4ed2f43c991aa08e2e1594103cb5f5bd7a7
ms.sourcegitcommit: 3eabd78305d32cd9b8a6bd1d76877ddc19d8ac63
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548885"
---
# <a name="gzip_decompress_from_base64_string"></a>gzip_decompress_from_base64_string()

Decodiert die Eingabe Zeichenfolge aus Base64 und führt gzip-decodeinkomprimierung durch.

## <a name="syntax"></a>Syntax

`gzip_decompress_from_base64_string("`*input_string*`")`

## <a name="arguments"></a>Argumente

*Input_string* : Eingabe `string` , die mit gzip komprimiert und dann Base64-codiert wurde. Die Funktion akzeptiert ein Zeichen folgen Argument.

> [!NOTE]
> Diese Funktion überprüft verbindliche gzip-Header Felder (id1, ID2 und cm) und gibt eine leere Ausgabe zurück, wenn eines dieser Felder falsche Werte hat.
> Optionale Header Felder werden nicht unterstützt. Sowohl FLG als auch XFL werden als Nullen erwartet.


## <a name="returns"></a>Gibt zurück

* Gibt eine zurück `string` , die die ursprüngliche Zeichenfolge darstellt. 
* Gibt ein leeres Ergebnis zurück, wenn Dekomprimierung oder Decodierung fehlgeschlagen ist 
    * Beispielsweise wird bei ungültigen gzip-komprimierten und Base 64-codierten Zeichen folgen eine leere Ausgabe zurückgegeben.

## <a name="examples"></a>Beispiele

```kusto
print res=gzip_decompress_from_base64_string("H4sIAAAAAAAA/wEUAOv/MTIzNDU2Nzg5MHF3ZXJ0eXVpb3A6m7f2FAAAAA==")
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

* Erstellen Sie eine komprimierte Eingabe Zeichenfolge mit [gzip_compress_to_base64_string ()](gzip-base64-compress.md).
* Siehe auch [zlib_decompress_from_base64_string](zlib-base64-decompress.md).
