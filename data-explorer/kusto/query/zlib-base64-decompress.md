---
title: zlib_decompress_from_base64_string ()-Azure Daten-Explorer
description: In diesem Artikel wird der zlib_decompress_from_base64_string ()-Befehl in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 09/29/2020
ms.openlocfilehash: e2163b3fe5460a660579889a589acd1e5fd965cd
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573512"
---
# <a name="zlib_decompress_from_base64_string"></a>zlib_decompress_from_base64_string ()

Decodiert die Eingabe Zeichenfolge aus Base64 und führt die zlib-Komprimierung aus.

> [!NOTE]
> Die einzige unterstützte Windows-Größe ist 15.

## <a name="syntax"></a>Syntax

`zlib_decompress_from_base64_string('input_string')`

## <a name="arguments"></a>Argumente

*Input_string*: Eingabe `string` , die mit zlib komprimiert und dann Base64-codiert wurde. Die Funktion akzeptiert ein Zeichen folgen Argument.

## <a name="returns"></a>Rückgabe

* Gibt eine zurück `string` , die die ursprüngliche Zeichenfolge darstellt. 
* Gibt ein leeres Ergebnis zurück, wenn Dekomprimierung oder Decodierung fehlgeschlagen ist 
    * Beispielsweise wird durch ungültige zlib-komprimierte und Base 64-codierte Zeichen folgen eine leere Ausgabe zurückgegeben.

## <a name="examples"></a>Beispiele

```kusto
print zcomp = zlib_decompress_from_base64_string("eJwLSS0uUSguKcrMS1cwNDIGACxqBQ4=")
```

**Ausgabe:**

| Test Zeichenfolge 123 |

Beispiel für ungültige Eingabe:

```kusto
print zcomp = zlib_decompress_from_base64_string("x0x0x0")
```

**Ausgeben**
||

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine komprimierte Eingabe Zeichenfolge mit [zlib_compress_to_base64_string ()](zlib-base64-compress.md).