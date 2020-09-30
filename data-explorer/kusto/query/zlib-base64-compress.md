---
title: 'zlib_compress_to_base64_string: Azure Daten-Explorer'
description: In diesem Artikel wird der zlib_compress_to_base64_string ()-Befehl in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 09/29/2020
ms.openlocfilehash: c43ffcbb449c6fc8a4c01b7a032df50c40829702
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573515"
---
# <a name="zlib_compress_to_base64_string"></a>zlib_compress_to_base64_string ()

Führt die zlib-Komprimierung aus und codiert das Ergebnis in base64.

> [!NOTE]
> Die einzige unterstützte Windows-Größe ist 15.

## <a name="syntax"></a>Syntax

`zlib_compress_to_base64_string('input_string')`

## <a name="arguments"></a>Argumente

*Input_string*: Eingabe `string` , eine zu komprimierende Zeichenfolge und Base64-codiert. Die Funktion akzeptiert ein Zeichen folgen Argument.

## <a name="returns"></a>Rückgabe

* Gibt einen zurück `string` , der zlib-komprimierte und Base64-codierte ursprüngliche Zeichenfolge darstellt. 
* Gibt ein leeres Ergebnis zurück, wenn die Komprimierung oder Codierung fehlgeschlagen ist

## <a name="example"></a>Beispiel

### <a name="using-kusto"></a>Verwenden von Kusto

```kusto
print zcomp = zlib_compress_to_base64_string("1234567890qwertyuiop")
```

**Ausgabe:** | " eaebfadr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGdw = = "|

### <a name="using-python"></a>Verwenden von Python

Die Komprimierung kann mit anderen Tools durchgeführt werden, z. b. python: 

```python
print(base64.b64encode(zlib.compress(b'<original_string>')))
```

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie [zlib_decompress_from_base64_string ()](zlib-base64-decompress.md) , um die ursprüngliche unkomprimierte Zeichenfolge abzurufen.